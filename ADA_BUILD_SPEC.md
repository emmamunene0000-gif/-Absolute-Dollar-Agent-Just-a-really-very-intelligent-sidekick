# ABSOLUTE DOLLAR AGENT — INTERNAL BUILD SPEC

> Working reference for the clean rebuild. Read alongside `CLAUDE.md` (doctrine)
> and `ADA_DEMO_DEPLOYMENT_PLAN.md` (test plan). This file is the surgical order
> of operations + the verification gates. The build is ONE continuous script;
> the "stages" below are internal checkpoints I self-verify against before the
> next section is written. Output: one compile-ready Pine v6 strategy.
>
> Identity: **Absolute Dollar Agent** — a really very intelligent sidekick.
> Not versioned as a personality. Glass Box always.

---

## A. FROZEN PAYLOAD GRAMMAR (verified against TradeSgnl's own AI — DO NOT drift)

One line, comma-separated, no spaces, no breaks. `LICENSE = 6678632939770`.
Distances carry `_points` uniformly (agent computes price distances; `bedist`
inherits type from `betrig_points`). `bedist=0` = breakeven exactly at entry.

```
LONG ENTRY
6678632939770,{{ticker}},buy,vol_dollar={{risk}},sl_price={{sl}},tp1_price={{tp1}},pct1=0.25,tp2_price={{tp2}},pct2=0.50,tp3_price={{tp3}},betrig_points={{betrig}},bedist=0,trtrig_points={{trtrig}},trdist_points={{trdist}},trstep_points={{trstep}}

SHORT ENTRY
6678632939770,{{ticker}},sell,vol_dollar={{risk}},sl_price={{sl}},tp1_price={{tp1}},pct1=0.25,tp2_price={{tp2}},pct2=0.50,tp3_price={{tp3}},betrig_points={{betrig}},bedist=0,trtrig_points={{trtrig}},trdist_points={{trdist}},trstep_points={{trstep}}

FLATTEN LONG (silence)          6678632939770,{{ticker}},closebuy
FLATTEN SHORT (silence)         6678632939770,{{ticker}},closesell

REVERSE LONG→SHORT (confluence + high ATR)
6678632939770,{{ticker}},closelongsell,vol_dollar={{risk}},sl_price={{sl}},tp1_price={{tp1}},pct1=0.25,tp2_price={{tp2}},pct2=0.50,tp3_price={{tp3}},betrig_points={{betrig}},bedist=0,trtrig_points={{trtrig}},trdist_points={{trdist}},trstep_points={{trstep}}

REVERSE SHORT→LONG (confluence + high ATR)
6678632939770,{{ticker}},closeshortbuy,vol_dollar={{risk}},sl_price={{sl}},tp1_price={{tp1}},pct1=0.25,tp2_price={{tp2}},pct2=0.50,tp3_price={{tp3}},betrig_points={{betrig}},bedist=0,trtrig_points={{trtrig}},trdist_points={{trdist}},trstep_points={{trstep}}
```

**Placeholder → source value (in f_format_alert):**
`{{risk}}` = `risk_per_trade` (the $15 — NOT `locked_actual_risk`).
`{{sl}}` = `locked_sl` · `{{tp1/2/3}}` = `locked_tp1/2/3` (absolute prices).
`{{betrig}}` = `|locked_tp1 − locked_entry|` (points to BE trigger @ TP1).
`{{trtrig}}` = `|locked_tp2 − locked_entry|` (points to trail activation @ TP2).
`{{trdist}}` / `{{trstep}}` = trail distance / step (ATR-derived, operator input).
NO TP1/TP2/TP3 partial messages exist. EA owns partials from the entry payload.

---

## B. SOURCE MAP (v7.0 `Agent_V7_Strategy_-_Tradesgnl.txt` → rebuild)

**KEEP AS-IS (untouched):**
- Section 1–2 constants/types/inputs (adjust only the TradeSgnl message inputs).
- Section 3 utilities (`_p`, `_pips`, `_pip_str`, `_get_contract_notional`,
  `_get_position_size`, `_size_display`, `_tg_json`). The notional router is the
  §3 single-source-of-truth — keep, verify per-symbol at runtime.
- Section 4–6 (Volume Profile, Adaptive VWAP, RSI) — VWAP now display-only.
- Section 8 Fractal 4-Layer state machine — the ATM fractal, ENTRY AUTHORITY.
  UNTOUCHED (dual-fractal doctrine: trail does not replace it).
- Section 11–13, 17–19 (Zones, Fib, SMC engine, Liquidity) — keep.
- Section 20 scoring + tree narrative — keep, extend for trail fractal.
- Section 21–24 (pip tracker, dpt_ arrays, dashboard, perf table) — keep,
  extend for RR ledger, correct metrics per §13.

**GRAFT (surgical change):**
- Section 7 ATM Bot: add `calcLiqTrail` (len 55, chart-TF, price-only). Join it
  into `regimeBullish/regimeBearish` as an AND leg. Retire `requireVWAP` gate.
- Section 14 risk locking: TP ratios stay 1 / 1.5 / 2. Add `betrig/trtrig/
  trdist/trstep` point-distance computation. `pct` = 0.25 / 0.50 / runner.
- Section 20/23/26: add TRAIL FRACTAL (M15/H1/H4 calcLiqTrail) beside ATM fractal.
- Section 30: REBUILD dispatch (see Stage 2).

**DELETE (these are the flaws):**
- Section 16 Holder Mode `strategy.close` path / `holder_exit_event` dispatch —
  the ghost runner. Holder Mode becomes REPORT-ONLY (narrates EA trail state).
- Section 30 lines 2776–2797 TP1/TP2/TP3 partial `strategy.close` calls (both
  sides) — the 400 spray source.
- Section 30 lines 2768, 2772 bare `strategy.close(...REVERSAL)` with no
  `alert_message` — the other 400 source.
- Section 26 standalone BOS/CHoCH + liquidity-sweep broadcasts (§10) —
  replaced by tree-card families A/B.

---

## C. GLOBAL INVARIANTS (must hold at EVERY stage — self-check each section)

```
 1. NO strategy.close() or entry EVER fires without a valid, non-empty
    alert_message. If a close can fire, it carries closebuy/closesell/
    closelongsell/closeshortbuy — never blank. (Kills the 400 spray.)
 2. TRAIL IS PRICE-ONLY. calcLiqTrail reads price, never posState / position /
    "am I in a trade". Gate may read trail; trail never reads gate. (posState
    circularity guard — the reason v8 was rejected.)
 3. AGENT LOT == EA LOT. vol_dollar carries risk_per_trade; the agent also
    computes locked_position_size from the SAME syminfo notional + SL distance.
    Dashboard shows the agent lot beside the risk $. If they'd diverge, the
    handshake is broken — this is the built-in glassbox test, not decoration.
 4. NO PINE PARTIAL LADDER. EA owns TP1/TP2/TP3 partials from the entry payload.
 5. RUNNER: last TP sent (tp3) closes remainder; runner rides trail toward it.
    Track how far past 2R it actually goes (RR ledger, §13).
 6. EAT (UTC+3) awareness for all scheduling/narration.
 7. GLASS BOX: every dashboard/card number traces to a real computed value.
    No simulated dollar P&L presented as truth (demote v7 min-unit $ sim).
 8. Entry gate consults the fractal/trail it computes. No machine-gun raw ATM.
```

---

## D. THE KILL LIST (must be provably gone by final assembly)

| Flaw (v7 evidence) | Removed by | Verify |
|---|---|---|
| 400 spray — message-less closes (lines 2768/72, 2776-97) | Stage 2 | Log shows ZERO `{{strategy.order.alert_message}}` across a session |
| `vol_dollar` wrong quantity (`locked_actual_risk`, line 2769) | Stage 2 | Payload `vol_dollar` = $15, agent lot = EA lot |
| Ghost runner (Section 16 Holder close) | Stage 5 | Dashboard never says "Holder Mode" while MT5 is flat |
| Machine gun (`long_entry_confirmed = buy_signal_confirmed`, ~2760) | Stage 1 | Signal count drops from ~15/day; entries require trail agreement |
| posState circularity | never introduced | calcLiqTrail body reads no position state |

---

## E. BUILD STAGES (internal checkpoints — one continuous script)

### STAGE 1 — SPINE (entry authority + clean payload)
**Objective:** trail-gated entries that dispatch verified, deliverable webhooks.
**Touches:** Section 7 (add `calcLiqTrail` len55, join to regime gate; retire
`requireVWAP`), Section 14 (BE/trail point distances; pct split), Section 30
(entry dispatch only, verified LONG/SHORT ENTRY strings).
**Doctrine:** §4.1, §4.2, §5.3, §6.1, §6.2.
**Verify before Stage 2:**
- [ ] `calcLiqTrail` reads price only (invariant 2).
- [ ] `regimeBullish = bullishRegime AND trail_up` (VWAP/Fib not gating).
- [ ] Entry fires only on ATM signal AND trail agreement (machine-gun fix).
- [ ] Entry payload matches frozen grammar byte-for-byte; `{{risk}}`=risk_per_trade.
- [ ] `betrig`/`trtrig` = correct point distances to TP1/TP2.

### STAGE 2 — HANDSHAKE HARDENING (kill the 400s)
**Objective:** no order event ever emits a blank/garbage webhook.
**Touches:** Section 30 (DELETE TP1/2/3 partial closes + bare reversal closes;
route reversal through `closelongsell`/`closeshortbuy` payload OR EA Exit-on-Entry
— pick Pine-explicit per §6.2), Section 3 `f_format_alert` (ensure risk arg =
risk_per_trade, add betrig/trtrig/trdist/trstep tokens).
**Doctrine:** §4.4, §4.5, §5.2, §6.2.
**Verify before Stage 3:**
- [ ] No `strategy.close` remains except full-flatten/reverse with valid message.
- [ ] No `qty_percent` partial closes anywhere.
- [ ] Agent-computed lot displayed = the lot the EA would derive from vol_dollar.
- [ ] Kill-list rows 1 & 2 provably gone.

### STAGE 3 — DUAL FRACTAL DISPLAY
**Objective:** ATM fractal (entry authority) + trail fractal (narrative) side by side.
**Touches:** Section 20 (add M15/H1/H4 `calcLiqTrail` reads), Section 23 dashboard
+ Section 26 cards (render both fractals; HTF trail = narrative only).
**Doctrine:** §5.3 (dual fractal), §9, §10.
**Verify before Stage 4:**
- [ ] ATM fractal (Section 8) unchanged and still entry authority.
- [ ] Trail fractal shown, never touches entry/exit path (invariant 8 respects ATM).
- [ ] HTF trails M15/H1/H4 report only.

### STAGE 4 — STRUCTURAL EXIT OVERLAY
**Objective:** confluence exit with conditional flatten-vs-reverse verb.
**Touches:** new detection (opposing CHoCH/BOS from Section 18 + opposing Fib
trend from Section 12) while in position; verb selection by ATR regime (Section 20
`atrHL`); Section 30 exit dispatch (flatten vs reverse strings).
**Doctrine:** §5.2 (verb selection is conditional, not linear).
**Verify before Stage 5:**
- [ ] Exit only fires in an active position (flat = normal entry).
- [ ] High ATR + opposing confluence → closelongsell/closeshortbuy.
- [ ] Low ATR / chop → closebuy/closesell (silence).
- [ ] Never a blind reversal; confluence required.

### STAGE 5 — RR LEDGER + GHOST REMOVAL + METRICS TRUTH
**Objective:** track real R extracted; remove ghost narration; correct metrics.
**Touches:** Section 16 (Holder Mode → report-only, delete close dispatch),
Section 21–22/28 (add peak R, final R, exit condition to dpt_ + daily report;
demote simulated $ P&L), Section 24 perf table (RR ledger).
**Doctrine:** §5.2 (ghost fix), §8 (memory), §13 (metrics correction).
**Verify (final gate):**
- [ ] No Holder Mode close dispatch; narration matches EA reality.
- [ ] Ledger records peak R, final R, exit price, exit condition.
- [ ] No simulated dollar figure presented as truth (pips/R are the ledger).

### FINAL ASSEMBLY
- [ ] Single Pine v6 strategy, compiles clean.
- [ ] All 8 invariants hold. Kill list fully cleared.
- [ ] `request.security` count reduced from 40+ (consolidate MTF EMA strip) —
      confirm under Pine's ceiling so the heavy script loads.
- [ ] Hands off to `ADA_DEMO_DEPLOYMENT_PLAN.md` Phase 0.

---

## F. OPEN INPUT — the one thing to confirm before Stage 1

**`calcLiqTrail` source.** The doctrine ports the len-55 chart-TF trail from TRON
GLASSBOX v3.0 (the green/red flip-diamond trail in your Image 5). That exact
function is NOT in my current context (only the full v7.0 source is). Before
Stage 1 I need EITHER:
- (a) the TRON `calcLiqTrail` function re-pasted, so I port it faithfully, OR
- (b) confirmation to define it as a len-55 ATR/range trail matching the flip
      behaviour visible in Image 5, which you then eyeball against TRON.

Everything else is fully specified and frozen. This is the only dependency.

*© 2026 Absolute Dollar Intelligence — Invite-only. Not financial advice.*
