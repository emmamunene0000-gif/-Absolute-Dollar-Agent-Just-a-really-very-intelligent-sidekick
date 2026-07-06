# ABSOLUTE DOLLAR AGENT — DEMO DEPLOYMENT & TEST PLAN

> Companion to `CLAUDE.md`. This is the pre-flight: what the live system is
> actually doing right now, what breaks, what to fix, and the exact order to
> test it on Deriv Demo (Volatility 10, Volatility 75, XAUUSD, GBPUSD).
> Every claim below is traced to a real line in `Agent_V7_Strategy_-_Tradesgnl.txt`
> or the live TradeSgnl alert log — nothing inferred.

---

## PART 1 — LIVE SYSTEM DIAGNOSIS (what the log + source actually show)

The agent is already live on XAUUSD and Volatility 10. The dashboards
(Images 3–5) prove the *reasoning* layer works — fractal sync, tree narrative,
liquidity, RR projections all render. But the TradeSgnl alert log (Image 2) is
red where it matters. Diagnosis:

### 1.1 THE 400 BAD REQUEST BUG — root-caused, not guessed

The log shows a clean split:
- `6678632939770,XAUUSD,buy,vol_lots=0.02,sl_price=...` → **delivered ✅**
- `6678632939770,XAUUSD,sell,vol_lots=0.02,sl_price=...` → **delivered ✅**
- `6678632939770,XAUUSD,closebuy,comment=...` → **delivered ✅**
- `{{strategy.order.alert_message}}` (literal) → **400 Bad Request ❌** (×many)

The failures are every order event that fires with an EMPTY or ABSENT
`alert_message`. Two sources in the source file:

- **Reversal pre-close (lines 2768, 2772):**
  `strategy.close("Short", comment="Short REVERSAL")` — NO `alert_message` at
  all. Fires on every reversal → TradingView sends the unresolved
  `{{strategy.order.alert_message}}` → 400.
- **TP partial ladder (lines 2776, 2779, 2782 + short mirror):**
  `strategy.close("Long", qty_percent=33.3, ..., alert_message=f_format_alert(long_tp1_message, ...))`
  where `long_tp1_message = ""` (line 386). `f_format_alert("")` returns `""`,
  so the fill fires with an empty message → 400. Same for TP2 (50%), TP3 (75%).

The intent was "leave TP messages blank so the EA handles partials." But
**blanking the message does not suppress the order-fill event** — TradingView
still fires the webhook, now with garbage. Every partial close and every
reversal is spraying a failed webhook. Entries and the explicit `closebuy`
exit (line 389) carry valid payloads, so those succeed. That's the exact
split the log shows.

This is the "order-fill noise from partial TP closes hitting the webhook"
flaw, caught red-handed.

### 1.2 THE FOUR LOAD-BEARING FLAWS — all confirmed in source

- **`vol_dollar` carries the wrong quantity.** Entry template (line 385) is
  `vol_dollar={{risk}}`, and the dispatch (line 2769) passes `locked_actual_risk`
  as the risk arg → `{{risk}}` resolves to the **min-unit estimate (~$3.38)**,
  not `risk_per_trade` ($15). The EA would size off the wrong number. (The live
  operator sidestepped this by hardcoding `vol_lots=0.02` in the deployed
  string — which is why the log shows fixed lots, not risk-based sizing.)
- **Ghost runner.** Section 16 runs a Pine-side Holder Mode after TP3 and fires
  `holder_exit_event` (a `strategy.close`). But the entry payload already told
  the EA to close everything at `tp3_price` (last TP = close-all). So Pine
  narrates and trails a 25% runner **the EA already flattened**. Dashboard says
  "Holder Mode"; broker is flat.
- **Machine-gun trigger.** `long_entry_confirmed = buy_signal_confirmed`
  (line ~2760) = ATM cross + RSI regime + `posState` flip only. `master_sync_buy`
  (the 4-layer fractal alignment, lines 912/924) is computed but used ONLY for
  the SYNC diamond and Telegram narrative — it **never gates the actual
  `strategy.entry`**. The agent fires on raw ATM+RSI and ignores the fractal it
  spent 40+ `request.security` calls computing. Image 3 shows the result: 15
  signals in one day on a 1-minute chart.
- **`posState` circularity** (why v8 was rejected): the Fractal 4-Layer
  (Section 8) reads `posState` across timeframes via `request.security`, and
  `posState` is driven by the filtered ATM signal. Any attempt to feed the gate
  back into `posState` closes a loop. The trail must stay price-only.

### 1.3 WHAT ACTUALLY WORKS (keep it)

The reasoning/narration layer is genuinely good and is NOT the problem: fractal
sync display, tree narrative, PDH/PDL context, liquidity swings, RR projection
lines (Image 5: TP1 1R / TP2 2R / TP3 3R off the trail), performance table.
Entries, `sell`, and `closebuy` deliver cleanly. The execution *plumbing* is
what's broken, not the brain.

> ⚠️ Note the RR labels: Image 5 shows TP1 **1R** / TP2 **2R** / TP3 **3R**,
> but the source and dashboard use TP1 1:1 / TP2 **1.5:1** / TP3 2:1. Two
> different ladders on screen. Confirm which is intended before the rebuild
> freezes the TP math (`CLAUDE.md` §6.2 currently locks 1 / 1.5 / 2 with the
> runner trailing past 2R). One-line decision.

### 1.4 PERFORMANCE / "HEAVY" IS REAL

The script issues 40+ `request.security` calls (3 fractal states, PDH/PDL, FVG,
3× drawLevels, 5× `get_narrative_status` fetching 5 series each, 9×
`emaTrend_tf` = 18 EMA fetches, 2× lower-tf). That's at or near Pine's
security-call ceiling — the reason it loads slowly and the reason the rebuild
should consolidate (the 9-timeframe EMA row alone is 18 calls for a cosmetic
strip). "Heavy" isn't just load time; it's a ceiling risk.

---

## PART 2 — THE FIX MAP (live flaw → CLAUDE.md doctrine → concrete change)

| Live flaw (source line) | Doctrine | Concrete change |
|---|---|---|
| 400s from TP partials (2776/79/82) | §4.5 | DELETE the Pine TP ladder entirely. EA owns partials via `tp1/pct1, tp2/pct2, tp3` in the entry payload. No partial order events → no 400s. |
| 400s from reversal pre-close (2768/72) | §5.2, §6.2 | DELETE bare `strategy.close(...REVERSAL)`. Reverse via the entry payload's exit-on-entry flag OR a valid `closelongsell`/`closeshortbuy`, chosen by the ATR-regime rule. Never a close with no `alert_message`. |
| `vol_dollar={{risk}}`→wrong $ (385/2769) | §6.1, §3 | Volume param carries `risk_per_trade` ($15), not `locked_actual_risk`. Exact param name = whatever the Syntax Generator emits (`risk=` / `vol_dollar=` / `vol_lots={{vol}}`). Test in Phase 1. |
| Ghost runner (Section 16) | §5.2 | Runner is EA-trailed (§6.2), not Pine-narrated. Pine's Holder Mode stops sending closes; it only *reports* the EA's trail state. |
| Machine gun (2760) | §4.1, §4.2 | Entry gate = ATM signal AND chart-TF trail agrees AND RSI regime. Trail joins the AND-gate; VWAP/Fib retire to display. Cuts the 15-signal spray. |
| Heavy / near security ceiling | (new) | Consolidate MTF security calls; drop the cosmetic 9-TF EMA strip or fetch it once. |

---

## PART 3 — PRE-MARKET SETUP (do before markets open)

### 3.1 EA-side (TradeSgnl portal — operator toggles, not Pine)
- **Trailing Mode = Signal** (else `trtrig/trdist/trstep` from the payload are
  ignored — the ghost-trade fix silently dies).
- **Parameter Source = Signal.**
- **Symbol Mapping — DONE** (Image 1 confirms: `VOLATILITY_75_INDEX → Volatility
  75 Index`, `XAUUSD → XAUUSD`, `VOLATILITY_10_INDEX → Volatility 10 Index`).
  GBPUSD not yet in the map — **add `GBPUSD → GBPUSD`** (or the broker's exact
  MT5 name) before testing it.
- **Exit on Entry** vs **Exit on Opposite** — decide the reversal mechanism:
  Exit-on-Entry (close opposite + open new) matches confluence-gated reversal;
  keep it OFF if the agent will send explicit reversal verbs instead. Pick one,
  don't run both.

### 3.2 Lock the payload from the generator (ground truth)
In *Custom Strategy Message*, fill the fields and copy the exact string with
License `6678632939770`. Confirm the two unknowns: volume param name, and
whether levels/distances need `_price`/`_points` suffixes or use the type
dropdowns. That string becomes the Pine alert template verbatim.

### 3.3 Per-instrument syminfo check (the §3 verification)
Before trusting sizing on each, confirm on the chart that `_get_contract_notional()`
resolves a sane `syminfo.pointvalue` / `syminfo.mintick`:

| Instrument | TV symbol | syminfo.type risk | Watch |
|---|---|---|---|
| Volatility 10 | `VOLATILITY_10_INDEX` | cfd/other → falls to `pv` | pointvalue must be real, not the `1.0` fallback |
| Volatility 75 | `VOLATILITY_75_INDEX` | cfd/other | large price (≈49k) — SL distance in points is big; check lot math |
| XAUUSD | `XAUUSD` | forex? or cfd? | v7 forces `100000` notional if type=="forex"; if Gold reports non-forex this branch is wrong — VERIFY |
| GBPUSD | `GBPUSD` | forex | the one true forex; `100000` notional + 0.0001 pip is correct here |

> The v7 `_get_contract_notional()` (line ~470) branches on `syminfo.type`.
> Deriv synthetics may report a type that hits the `pv` fallback — fine IF
> `syminfo.pointvalue` is real, dangerous if it's the `1.0` default. This is the
> first thing to eyeball per symbol.

---

## PART 4 — PHASED DEMO TEST SEQUENCE

Run in order. Do not advance a phase until the prior one is green. Use **minimum
risk** ($5–15) and Deriv **Demo** throughout. One instrument at a time.

### Phase 0 — Handshake smoke test (Volatility 10, ~15 min)
- Fire ONE manual `buy` via the generator's Test Signal → confirm it lands as a
  demo position on MT5. This isolates the wire (TV → webhook → EA → MT5) from
  the agent logic.
- **Green =** position opens on demo, correct symbol, correct direction.
- **Watch:** the alert log shows delivered ✅, not 400.

### Phase 1 — Entry + sizing (Volatility 10, then XAUUSD)
- Let the agent fire a real entry. Verify the payload carries `risk_per_trade`
  ($15), not $3.38, and that the EA's resulting lot is sane for the SL distance.
- Cross-check: dashboard "Variable: $15 → N Units" vs the actual MT5 lot.
  **They must agree** — that's the §13 glassbox test. If they diverge, the
  handshake is broken (debug sizing first, not the brain).
- Repeat on XAUUSD (your key instrument) — confirm Gold's notional branch.

### Phase 2 — BE + trailing = the ghost-trade fix (Volatility 10 or 75)
- Ride a winner to TP1: confirm SL moves to breakeven (`betrig`/`bedist` at TP1).
- To TP2: confirm EA trailing activates (`trtrig`/`trdist`/`trstep`).
- Confirm the runner **exits on the trail**, not by round-tripping to entry.
  This is THE thing you built the rebuild for — watch one full trade do it.
- **Watch:** NO `{{strategy.order.alert_message}}` 400s during the trade. If
  they appear, the TP ladder wasn't fully removed.

### Phase 3 — Structural exit + verb selection (any liquid instrument)
- Force a scenario where opposing CHoCH/BOS + opposing Fib forms mid-trade.
  Confirm the agent sends a single real close.
- Verify verb choice: high ATR + opposite confluence → `closelongsell`
  (reverse); low ATR / chop → `closebuy` (flatten). Watch it pick correctly.

### Phase 4 — Multi-instrument + GBPUSD (only after 0–3 green)
- Add Volatility 75 and GBPUSD. GBPUSD is the forex control — if sizing/pips are
  right on Gold + a synthetic + real forex, the cross-instrument `_points`/
  notional logic holds. Watch GBPUSD pip vs point handling specifically.

---

## PART 5 — GO / NO-GO CHECKLIST

**GO to live seed only when ALL are true:**
- [ ] Alert log shows ZERO `{{strategy.order.alert_message}}` 400s across a full session.
- [ ] Dashboard lot = MT5 lot (sizing glassbox holds) on all 4 instruments.
- [ ] BE-at-TP1 and trail-from-TP2 observed end-to-end on ≥2 real trades.
- [ ] Runner exits on trail, never round-trips to entry (ghost-trade fixed).
- [ ] Structural exit fires a valid close (never a bare, message-less close).
- [ ] Reversal verb matches ATR regime (flatten vs reverse) at least once each.
- [ ] Signal count dropped from ~15/day toward selective (trail gate working).
- [ ] Symbol Mapping resolves for all 4 (GBPUSD added).

**NO-GO signals to stop and debug:**
- Any 400 in the log → a close is firing without a valid `alert_message`.
- Dashboard "Holder Mode" while MT5 is flat → ghost runner not removed.
- Lot on MT5 ≠ dashboard lot → sizing param wrong (`risk`/`vol_dollar`/`vol_lots`).

---

## PART 6 — OPEN ITEMS (decide before the rebuild freezes)

1. **TP ladder ratios:** 1 / 1.5 / 2 (source + dashboard) vs 1 / 2 / 3R
   (Image 5 trail projection). `CLAUDE.md` §6.2 locks 1 / 1.5 / 2 + trailed
   runner. Confirm.
2. **Volume param:** `risk=` (assistant) vs `vol_dollar=` (source) vs
   `vol_lots={{vol}}` (live log, agent-computed). Generator output decides;
   Phase 1 verifies sizing. Recommendation: whichever produces correct lots on
   Deriv demo across all 4 — lean `vol_dollar=risk_per_trade` if the EA sizes
   right (most glassbox-consistent with §6.1), fallback `vol_lots={{vol}}`.
3. **Reversal mechanism:** EA "Exit on Entry" flag vs Pine-explicit
   `closelongsell`/`closeshortbuy`. Pick one to keep the handshake unambiguous.
4. **noexit persistence:** verify with a Test Signal before relying on any
   EA-managed-exit mode.

---

## THE ONE-LINE SUMMARY

The brain works; the plumbing sprays 400s. The rebuild's job is not to make the
agent smarter — it's to stop it sending message-less closes, stop it narrating
positions the EA already flattened, size off the right dollar, and consult the
fractal it already computes before it fires. Fix the handshake, and the agent
you already see on the chart becomes the agent that actually trades the demo.

*© 2026 Absolute Dollar Intelligence — Invite-only. Not financial advice.*
