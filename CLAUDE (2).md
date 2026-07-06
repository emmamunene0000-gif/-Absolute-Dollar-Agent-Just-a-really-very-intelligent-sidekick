# ABSOLUTE DOLLAR AGENT — BUILD CHARTER (`CLAUDE.md`)

> **Lock-in reference. Read this whole file before writing or touching any Pine.**
> This is the single source of truth for the build. Where this file and any prior
> code (v7.0 base, v8.0 graft attempt) disagree, **this file wins.**

---

## THE FORMULA — the operating philosophy, not a slogan

```
 Analysis + Capital + Execution = Liquidity Extraction

 ├── Analysis  — what the fractal layers, structure, and confidence score
 │               ACTUALLY say right now. Not "what I hope" — what's measured.
 ├── Capital   — the fixed risk_per_trade and real syminfo-derived sizing.
 │               Capital exposure is a CONSTANT, not a variable that flexes
 │               with excitement.
 ├── Execution — the ATM-primary, trail-secondary handoff to TradeSgnl.
 │               Clean, undisputed, one signal wins.
 └── = Liquidity Extraction — the actual job. Not "being right about
       direction," but extracting the move already telegraphed by structure,
       sessions, and liquidity pools. An intraday momentum trader doesn't
       predict — it recognises displacement and rides the wake.

 Self-concept: Absolute Dollar Agent is a Decision Support System (DSS) —
 augmented intelligence, not autonomous conviction. It surfaces the read,
 shows its work, and either the operator or TradeSgnl's rules act on it.
 It never hides the "why."

 On Pine vs Python: Pine can't train models or backprop, and it shouldn't try.
 Its edge is deterministic, bar-by-bar, glassbox scoring that never drifts or
 needs retraining, running natively where price actually ticks. The
 "intelligence" is weighted confluence scoring, regime-quality classification,
 and structural memory (swing/session/liquidity state in var) — not learned
 weights. That's a feature. It's why the dashboard can say "score 11/15,
 SOVEREIGN alignment, 3 of 4 layers agree" and mean something falsifiable,
 bar by bar, forever.
```

---

## 0. Identity — say this out loud before you build anything

```
 ├── NOT Jarvis. Don't abbreviate it, don't reference it as a product name.
 ├── NOT versioned as a personality — no "v8", no "v71" as an identity.
 │    Version numbers track code changes. They are NOT the agent's name.
 ├── IS: "Absolute Dollar Agent" — Just a really very intelligent sidekick.
 └── End-state engineering shorthand ONLY (never user-facing): "TronJarvis"
       ├── TRON half   = deterministic, glassbox, stateless price DETECTION
       │                 (Pine as the execution spine — it detects, it does
       │                 not narrate a story about what it detects)
       └── JARVIS half = the intelligence layer that turns detection into
                          natural language, holds context, and is aware of
                          itself, aware of price, and can reason about both.
      One agent. Not two products bolted together. One voice at the end.
```

---

## 1. Tech Stack & Brokers — the world this agent lives in

```
 ├── Platform   : TradingView Premium (sandboxed as a Strategy script)
 ├── Execution  : TradeSgnl Advanced — the agent's personal execution arm.
 │                TradingView is the brain/eyes. TradeSgnl is the hands.
 │                Built SPECIFICALLY for execution via TradeSgnl — not a
 │                generic webhook target.
 ├── Messaging  : Telegram (dual channel — War Room / Public)
 ├── Scheduling : East Africa Time (UTC+3) is a HARD requirement for all
 │                scheduling and narration. The operator runs on EAT.
 └── Brokers (priority order)
      ├── Deriv       — PRIMARY. CFD/synthetics. Deriv assets are natively on
      │                 TradingView, so syminfo.* reads their real tick size /
      │                 point value directly. No guessing.
      ├── Pepperstone — forex
      └── Bybit       — perpetual futures
      Note: "even on MT5 it's the same broker, so nothing different." The
      underlying instrument truth (tick size, contract size, point value) does
      not change with which terminal displays it. syminfo.* on TradingView and
      the MT5 symbol spec must agree — same broker-side contract spec.
```

---

## 2. Base Codebase Doctrine

```
 ├── ADA v7.0 IS THE BASE. It does not get rebuilt. Untouched, as-is:
 │    ├── Fractal 4-layer state machine (Sovereign/Anchor/Filter/Exec)
 │    ├── SMC engine (BOS/CHoCH/OB/FVG/EQH-EQL/Premium-Discount/Liquidity)
 │    ├── Volume Profile engine
 │    ├── Platinum Risk Model (asset-routed sizing)
 │    ├── Trade ID engine
 │    └── Dashboard skeleton (table layout, placement, styling)
 └── The mistake to never repeat: rewriting from scratch produces a different
      agent by accident. The correct move is always GRAFT, not REPLACE. Every
      patch below is a diff against v7.0, not a new file.

 ── posState CIRCULARITY GUARD (why v8 was rejected as-patched) ──
 The trail (calcLiqTrail) MUST be price-only. It reads nothing from posState.
 The regime gate may READ the trail; the trail may NEVER read the gate, the
 position state, or "am I in a trade". If any graft reintroduces that loop,
 the graft is wrong — stop and re-cut it clean.
```

---

## 3. Contract Notional — real symbol value routing

```
 ├── Dollar calc and lot sizing MUST derive from the real symbol's tick value
 │    and point value — never a hardcoded forex/crypto/futures guess table.
 ├── Deriv assets live on TradingView with their own syminfo.pointvalue /
 │    syminfo.mintick — use those directly for CFD/synthetics.
 ├── Same principle across Pepperstone (forex) and Bybit (perps) — one router,
 │    fed by real syminfo, not by syminfo.type branching assumptions alone.
 └── This is the existing _get_contract_notional() single-source-of-truth
      pattern from v7.0 — KEEP IT, verify it reads LIVE syminfo values for
      every asset in the watchlist (§12), not falling back to a stale default
      silently.
```

---

## 4. The Graft List — what actually changes in v7.0

```
 ├── 4.1 Regime Gate Rework
 │    ├── calcLiqTrail (chart-TF only, ported from TRON) becomes one leg of an
 │    │    AND-gate joined with RSI in regimeBullish / regimeBearish.
 │    └── VWAP retires from the gate → DISPLAY-ONLY. Still shows on the
 │         dashboard/narrative; no longer blocks or permits a trade by itself.
 │
 ├── 4.2 Entry Trigger Widened
 │    └── Entry = buy_signal OR flip_bull (ATM cross OR chart-TF trail flip),
 │         filtered through the SAME regime gate. This widening does NOT mean
 │         a trail flip may independently open a trade while one is already
 │         running — read §5 before wiring this.
 │
 ├── 4.3 Smart Momentum — Scale-In Only
 │    └── Sustain + M5 confirm acts ONLY as a scale-in trigger, never a fresh
 │         entry. Must be trail-gated AND leg-capped (hard ceiling on stacked
 │         scale-in legs). Each leg is an INDEPENDENT trade — no py pyramid flag.
 │
 ├── 4.4 Bridge Fixes (alert payload correctness)
 │    ├── Alert dollar-risk field uses risk= carrying risk_per_trade, NOT
 │    │    locked_actual_risk (locked_actual_risk stays display-only; not sent).
 │    │    Param name is risk= per the TradeSgnl generator — not vol_dollar=.
 │    ├── betrig / trtrig computed from TP1/TP2 distance, not hardcoded.
 │    ├── TP3 is NOT a hard close-all — the 25% runner rides the trail past 2RR
 │    │    to a DISTANT operator-set ceiling (§6.2); RR-at-exit is tracked (§13).
 │    └── Reversal verbs: closelongsell / closeshortbuy (fired conditionally, §5.2).
 │
 └── 4.5 Deletions
      └── The local TP1/TP2/TP3 partial-close ladder is DELETED from Pine.
           TradeSgnl owns partial-close logic — Pine does not duplicate it.
```

---

## 5. Execution Doctrine — the part that had to be argued out

```
 ├── 5.1 Hedging: NO. One position at a time. Long+short simultaneously is
 │        explicitly rejected. "Silence is a position." Standing aside when
 │        conditions don't align IS the trade being taken.
 │
 ├── 5.2 EXIT DOCTRINE — RESOLVED. It is (a) AND (b), not either.
 │    ├── (a) Operator co-manages. The operator actively manages trades on the
 │    │       broker side too. The agent does NOT hold sole exit responsibility.
 │    ├── (b) Agent owns a CONFLUENCE-based structural exit — never a blind
 │    │       reversal. In a long: an opposing CHoCH/BOS WITH an opposing Fib
 │    │       trend forming against the position → EXIT. Mirror for shorts.
 │    │       Reversal signals ALONE never flip; confluence earns the exit.
 │    │       ("you get sniper entries, but with confluence you gain the
 │    │        confidence to deploy.")
 │    ├── VERB SELECTION IS CONDITIONAL, NOT LINEAR — price is fractal, so the
 │    │       flatten-vs-reverse choice reads the regime. ONLY relevant when in
 │    │       an active position (flat = normal entry, none of this applies):
 │    │         • High ATR + confluence to deploy the opposite → REVERSE:
 │    │           closelongsell / closeshortbuy (close AND open opposite).
 │    │         • Low ATR / chop / no confluence → FLATTEN: closebuy / closesell
 │    │           (silence is a position — don't get chopped reversing into noise).
 │    │       closelongsell is NOT to be blanket-avoided — it is the CORRECT verb
 │    │       when the regime earns the reversal. It just opens a short, so only
 │    │       fire it when the short is actually justified.
 │    ├── GATE LESSON (locked): requireVWAP / requireFibTrend as BINARY blockers
 │    │       FAILED. require-TRAIL is what worked → the trail is the AND-gate
 │    │       leg (§4.1); VWAP/Fib are display-only confidence, never blockers.
 │    └── THE GHOST-TRADE FIX — the exit that actually matters. Two layers:
 │         The EA holds only entry/SL/TP. A trade runs into profit (peak pips ≥
 │         ATR range), then price round-trips to entry, hits SL or a ghost fill,
 │         and hands the profit back. FIX =
 │         ├── EA mechanical layer: BE@TP1 → distance-trail from TP2 → TP3=2RR
 │         │    final close-all. The runner rides the trail, doesn't decay back
 │         │    to entry. (§6.2 grammar.)
 │         └── AGENT structural overlay: at ANY point in the position, opposing
 │              confluence fires a SINGLE real close (flatten or reverse per the
 │              conditional verb rule above) that flattens whatever remains
 │              BEFORE the EA's levels are hit. One close, not a partial ladder —
 │              §4.5 stays intact. EA trails to a 2RR cap; agent flattens early
 │              when structure violently opposes. Both, not either.
 │
 └── 5.3 Signal priority — DO NOT MIX ENTRY LOGIC
      ├── ATM bot signal = PRIMARY entry trigger. This is what opens a trade.
      ├── Chart-TF trail flip (len 55) = CONTRIBUTES to entry conditions at the
      │    time the alert is sent, but does not dispatch orders independently.
      │    55 is chosen deliberately: intraday, tuned to avoid getting cut a
      │    thousand times until the account bleeds. Its jobs:
      │    ├── Position OPEN + trail flips → EXIT (regime death).
      │    └── NO position open + trail flips → MAY serve as entry (only when
      │         flat), so the trail and ATM bot don't fight over "the" entry
      │         on the same bar.
      ├── HTF trails (M15/H1/H4) = NARRATIVE/REPORTING ONLY (§7, §10). They are
      │    price-conditions-at-time-of-analysis for the Telegram cards and
      │    dashboard. They NEVER touch the entry or exit path.
      ├── DUAL FRACTAL — the trail does NOT replace the ATM bot's fractal. The
      │    build carries TWO parallel fractal reads that SHARE the dashboard and
      │    the cards:
      │      • ATM fractal  = the existing Section 8 posState state machine
      │        (Sovereign/Anchor/Filter/Exec). Untouched. The ENTRY authority.
      │      • Trail fractal = calcLiqTrail trend projected across M15/H1/H4.
      │        NARRATIVE only — reports, never triggers. Shown beside the ATM
      │        fractal so the operator sees both truths at a glance.
      │    "Narrative-only" means it reports, not that it is absent.
      └── "for order to be sent, priority is the ATM bot... to avoid mixing
           entry logic."
```

---

## 6. Risk & TP/SL Doctrine

```
 ├── 6.1 Risk per trade: FIXED dollar amount (risk_per_trade), sent to the EA
 │        via the risk= param. NOT scaled with confidence. Confidence decides
 │        whether a trade fires at all (via the gate) — it does not resize it.
 │
 └── 6.2 TP structure: TP1 = 1:1, TP2 = 1.5:1, TP3 = 2:1 — UNCHANGED ratios.
      ├── GROUND TRUTH = the TradeSgnl Syntax Generator ("Custom Strategy
      │    Message"), NOT assumed grammar. Fill the fields, copy the exact string
      │    it emits with the real License ID, and the Pine template fills THAT.
      │    (Earlier vol_dollar/_points wording was inference, since corrected.)
      ├── Volume param is risk= (per TradeSgnl assistant + generator), carrying
      │    the risk_per_trade dollar value. NOT vol_dollar=. NOT locked_actual_risk.
      ├── Payload is ONE line, comma-separated key=value, no spaces, no breaks.
      ├── Partials owned by the EA: pct1=0.25 @ TP1, pct2=0.50 @ TP2, remaining
      │    0.25 = RUNNER. The runner is NOT hard-capped at 2RR — it RIDES THE
      │    TRAIL past 2RR to capture extended moves (operator manages manually,
      │    so let it breathe). Resolves §14 TP3 → trailed runner, not a cap.
      ├── RUNNER WIRING (robust to the last-TP-closes-all uncertainty): send tp3
      │    as a DISTANT operator-set ceiling (input "runner_ceiling_rr", default
      │    ~5RR), so the EA distance-trail is the REAL exit in ~every case and tp3
      │    is only a far backstop. Verify the exact last-TP-closes-all behaviour
      │    with a Test Signal; tighten or drop the distant tp3 once confirmed.
      │    Effectively uncapped intraday, never undefined.
      ├── Levels: absolute PRICES (sl / tp1 / tp2 / tp3), agent-computed from real
      │    syminfo (§3). Distances for BE/trailing: betrig / bedist / trtrig /
      │    trdist / trstep. TYPE is set in the generator's per-field dropdowns —
      │    set SL/TP type = Price, BE/Trailing type = Points — OR use the explicit
      │    _price / _points suffixes the generator supports. Confirm which form
      │    the generator emits and match it EXACTLY; do not hand-edit the type.
      ├── Ghost-trade fix (§5.2): betrig = |TP1-entry|, bedist = 0 → BE at TP1.
      │    trtrig = |TP2-entry|, trdist / trstep → distance-trail from TP2 that
      │    rides the runner up with NO upper cap. REQUIRES EA "Trailing Mode =
      │    Signal" (else trail params ignored).
      ├── RR + EXIT TRACKING (§13 truth-metrics): on runner exit the agent records
      │    PEAK RR reached, FINAL RR at exit, exit price, and EXIT CONDITION
      │    (trail stop-out / structural exit / SL / distant-TP3). RR is the honest
      │    ledger — "how many R we extracted and why it ended." Feeds dashboard,
      │    cards, daily digest.
      ├── Agent structural exit (§5.2) fires as a REAL close on opposing confluence
      │    — closelongsell/closeshortbuy to reverse, closebuy/closesell to flatten
      │    — chosen by the ATR-regime rule. This is the early overlay on top of the
      │    EA ladder; it is NOT filtered (do not wrap it in an "ignore exits" flag).
      ├── noexit / "Ignore Exit Signals": DEFAULT OFF. §4.5 already removed the
      │    Pine partial ladder, so there is no stray-close noise to suppress, and
      │    the agent's structural close MUST be allowed through. Verify behaviour
      │    with the generator's Test Signal before adopting any EA-managed-exit mode.
      ├── EA-side (operator toggles, not Pine): Trailing Mode = Signal; Parameter
      │    Source = Signal; Symbol Mapping DONE (operator has mapped TV→MT5 names,
      │    e.g. VOLATILITY_75_INDEX → "Volatility 75 Index", XAUUSD → XAUUSD).
      └── Reversal handling: prefer Pine-explicit verbs over EA "Exit on Entry"
           toggle, to keep the handshake unambiguous — one alert, Pine decides.
```

---

## 7. Scheduled Intelligence Cadence — all times EAT (UTC+3)

```
 ├── Automatic broadcast — not manual, not on-demand only.
 ├── "24 hours = 1 candle" — the agent breaks a single day into a tactical,
 │    intraday narrative as the day progresses.
 └── Trigger points for scheduled (Family A) broadcasts:
      ├── H4 candle close
      ├── H1 candle close
      └── Session opens (Tokyo / London / New York)
      This matches Section 20's existing tree_narrative shape in v7.0 — the
      graft makes that tree fire on a SCHEDULE (Family A) IN ADDITION to firing
      on events (Family B), not replacing one with the other.
```

---

## 8. Memory Doctrine

```
 ├── Bot state: DAILY RESET. No persistent multi-day position/state memory.
 └── "Episodic memory" is NOT a database of past days — it's the agent
      externalising its own live data points into natural-language one-liners
      AS IT GOES. The memory is EXPRESSED, not stored. Every dashboard line and
      every Telegram card is the agent narrating its own current read of itself
      and of price, in plain language, in real time. That narration IS the
      "episodic" layer — reset fresh each day alongside the pip tracker / daily
      report engine already in v7.0 (Sections 21–22).
```

---

## 9. TRON Alignment — what we take, what we deliberately leave behind

```
 TRON was supplied so Claude can align vision, not so its architecture gets
 ported wholesale.

 ├── KEEP: "Pine is the Deterministic Execution Spine. It DETECTS — trend,
 │    structure, momentum, liquidity, confidence — and emits clean, stateless,
 │    self-aware signals." Right posture for the DETECTION layer.
 ├── KEEP: every alert payload carries enough architecture-state context
 │    (fractal alignment, structure, confidence breakdown) that a reader —
 │    human or machine — can reconstruct WHY a signal fired without querying
 │    Pine again. Glassbox, not a black box.
 ├── KEEP (as inspiration): stateless MTF trail (calcLiqTrail), regime-strength
 │    scoring, sync-layer counting, confidence engine as a weighted, thresholded
 │    score rather than a binary flag.
 └── LEAVE BEHIND / DO NOT PORT DIRECTLY:
      ├── TRON's stance that Pine tracks ZERO position state does NOT fully
      │    apply. There is no external Python "Brain" between Pine and the EA
      │    here — Absolute Dollar Agent bridges DIRECTLY into TradeSgnl and must
      │    retain enough local state to manage its trade phase, TP-ladder
      │    context, and trail hand-off. THE HANDSHAKE IS THE MOST IMPORTANT PART
      │    OF THIS WHOLE PROCESS, and it requires Pine to know some things about
      │    its own trade state.
      └── TRON's options/strike/expiry math (vanilla options, delta approx, IV
           proxy) is NOT part of this build. Deriv CFD / forex / perps need no
           strike/expiry logic. Leave it out entirely.
```

---

## 10. Telegram Doctrine

```
 ├── v7.0 Section 26 (SL-autopsy wall, full narrative dump, PF/WR block) —
 │    TORN OUT. Standalone BOS/CHoCH and liquidity-sweep broadcasts — KILLED.
 └── Replaced with tree-card format, two families:
      ├── FAMILY A — Scheduled (§7 cadence: 4H/H1/M15/M5 tree, session opens).
      │    The routine "here's what I see" check-in, not tied to a trade event.
      └── FAMILY B — Moment Events: entry, exit, TP1/BE, TP2/trail-start, TP3
           ride, scale-in added, regime-flip exit, rejection.
      Both families render:
      ├── War Room (Chat 1) → FULL glassbox detail (levels, points, take)
      └── Public   (Chat 2) → SANITIZED (direction + bias + session, no exact
           levels) — same sanitize toggle pattern from v7.0, re-applied to the
           new tree-card format instead of the old flat block.
```

---

## 11. Meta-Cognition — the micro-intelligence structure

```
 ├── The agent remains, fundamentally, a signal generator. That does not
 │    change. What changes: it also behaves like a personal trading assistant —
 │    without narrating "how" it works, only "what it sees" and "what it's doing
 │    about it."
 ├── Internally the build reads like a set of small, named micro-intelligences
 │    (regime gate, confidence scorer, trail state, structural context, session
 │    context, risk state) — each computing its own small piece of truth.
 └── Externally, ALL those data points get converted to natural language before
      they ever reach a human. Dashboard and Telegram are two renderings of the
      same self-narration. No raw score dumps without a plain-language sentence
      attached; no plain-language sentence that isn't backed by a real computed
      value.
```

---

## 12. Watchlist — the "Red List" (resolves §3's contract-notional check scope)

```
 Every instrument below MUST resolve a correct syminfo.pointvalue /
 syminfo.mintick before _get_contract_notional() is trusted for sizing. Note the
 magnitude spread (VOL 25 1S ~815,795 vs XRP ~1.13) — this is exactly why a
 hardcoded notional table breaks and live syminfo is mandatory.

 ├── Deriv synthetics
 │    ├── VOLATILITY 10 INDEX
 │    ├── VOLATILITY 25 INDEX
 │    ├── VOLATILITY 75 INDEX
 │    ├── VOLATILITY 10 (1S) INDEX
 │    ├── VOLATILITY 25 (1S) INDEX
 │    ├── VOLATILITY 75 (1S) INDEX
 │    ├── STEP INDEX
 │    ├── STEP INDEX 500
 │    └── RANGE BREAK 100 INDEX
 ├── Forex / metals
 │    ├── XAUUSD   (appears twice — two feeds/brokers; verify each resolves)
 │    ├── XAGUSD
 │    ├── GBPUSD
 │    ├── EURGBP
 │    └── EURAUD
 ├── Commodity
 │    └── WTI
 ├── Index
 │    └── US TECH 100
 └── Crypto perpetuals (Bybit)
      ├── BTCUSDT.P
      ├── ETHUSDT.P
      └── XRPUSDT.P

 The 1-second (1S) volatility indices tick on a different cadence — confirm
 mintick/pointvalue specifically for these, don't assume they match their
 standard-cadence siblings.
```

---

## 13. Definition of Done / Verification

```
 ├── No separate "success metric" is invented for this build. Verification is:
 │    ├── What the dashboard displays in real time (glassbox — if the dashboard
 │    │    says it, it must be traceable to a real computed value), AND
 │    └── What actually executes and closes on MT5 via TradeSgnl.
 │    If those two don't agree, THE HANDSHAKE IS BROKEN — that's the first thing
 │    to debug, not the detection logic.
 │
 └── METRICS CORRECTION — track truth, not a flattering fiction.
      ├── The agent does NOT compute real money. The EA/broker owns dollars via
      │    vol_dollar. v7.0's simulated min-unit-dollar P&L is the flawed number
      │    — it presented an estimate as if it were truth. Demote or drop it.
      ├── The agent tracks what it actually controls and can verify: PIPS, RR
      │    achieved, and signal outcomes (TP1/2/3/SL, peak pips). These are real.
      ├── RUNNER LEDGER (added): peak RR, final RR at exit, exit price, and exit
      │    condition (trail stop-out / structural exit / SL / distant-TP3). "How
      │    many R did we extract and why did it end" — the operator's real metric.
      └── "Absolute Dollar" is the DISCIPLINE that pips equal real money
           downstream (100 pips on 0.01 XAUUSD = $10) — not the agent faking a
           dollar figure on the panel. Net pips is the honest ledger.
```

---

## 14. Threads — status

```
 ├── §5.2 Exit mechanism ........... ✅ RESOLVED. Operator co-manages; agent owns
 │        confluence structural exit with CONDITIONAL flatten-vs-reverse (ATR
 │        regime picks the verb); EA ladder + trail = ghost-trade fix.
 ├── HTF trail role ............... ✅ RESOLVED. Dual fractal; trail narrative-only.
 ├── TP3 modeling ................. ✅ RESOLVED. 25% @ TP1, 50% @ TP2, 25% RUNNER
 │        rides the trail PAST 2RR to a distant operator-set ceiling; RR-at-exit
 │        + exit-condition tracked. EA owns the ladder (§4.5 intact).
 └── Payload string .............. ⏳ LAST CONFIRM (operator, 30 sec). Generate the
          exact template from TradeSgnl's "Custom Strategy Message" with fields
          filled, and confirm: (1) volume param = risk= , (2) whether levels/
          distances carry explicit _price/_points suffixes or rely on the type
          dropdowns. The Pine template will mirror that string EXACTLY. Nothing
          else blocks the build.
```

---

## Status of prior code
- **v7.0** — the base. Stays. Everything above grafts onto it.
- **v8.0** — the graft *attempt*. Useful reference for how the graft can look, but
  it is **not** authoritative. Any conflict between v8.0 and this charter resolves
  in favour of this charter, and the §14 threads must be closed by the operator
  before v8.0's choices on them are trusted.

*© 2026 Absolute Dollar Intelligence — Invite-only. Not financial advice.*
