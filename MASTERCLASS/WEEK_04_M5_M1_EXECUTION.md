# WEEK 4 — M5/M1 EXECUTION + DASHBOARD MASTERY
## The Execution Week | The trigger layer. Where the trade is born.

---

> **"On M1 and M5 we are interested in the core signals on the dashboard.
>  We want to confirm a buy with conditions — manual confluence, confidence
>  of price delivery."**

---

## WEEK 4 MASTERY GATE

By end of Day 5 this week, the operator must be able to:
- Read the full dashboard in under 60 seconds and make an EXECUTE or SKIP decision
- Understand every core signal on M5: ATM, Regime, V.WAP, Fib Trend
- Execute the full confluence gate (6-check system) before every trade
- Apply the TP protocol correctly: TP1 → TP2 → TP3 → Holder Mode
- Complete a live trade from signal to exit using the execution card

---

## DAY 1 — MONDAY | CONCEPT: THE M5 SIGNAL LIFECYCLE

### From Scan to Execution — The Full Signal Path

```
[1] Agent scans: ATM trailing stop crosses (M5)
         ↓
[2] Regime filter: is RSI momentum + VWAP aligned with signal direction?
         ↓ (if blocked → Rejected X, stop here)
[3] buy_signal_confirmed / sell_signal_confirmed fires
         ↓
[4] Fractal 4-layer check: L1+L2+L3+L4 state machine evaluates
         ↓
[5] Agent determines sync phase:
    FULLY ALIGNED (4-layer) → master_sync_buy / master_sync_sell fires (diamond label)
    COUNTER-TREND → CT-L or CT-S fires (gold triangle)
    PARTIAL ALIGNMENT → "Buy" or "Sell" label only (L4 local signal)
         ↓
[6] Glass Box alert fires → dashboard updates → Telegram broadcast
         ↓
[7] Trade locks: entry, SL, TP1, TP2, TP3, position size computed
         ↓
[8] Operator evaluates → EXECUTE or SKIP
```

### The Three Signal Types on the Chart

**Master Sync (Diamond label) — Highest quality**
```
SYNC BUY  = blue/green diamond below bar
SYNC SELL = blue/green diamond above bar
```
All 4 layers aligned. Score typically ≥ +6. Operator should strongly consider.

**Local Signal (Buy/Sell label) — Standard quality**
```
Buy  = small green label below bar
Sell = small red label above bar
```
L4 exec confirmed but not all 4 layers aligned. Operator evaluates score and context.

**Counter-Trend Signal (CT-L / CT-S) — Flagged quality**
```
CT-L = gold triangle below bar
CT-S = gold triangle above bar
```
Signal fires against Daily sovereign direction. Operator applies extra scrutiny.

### What the Barcolor Tells You

The chart bars change color when a signal is active:
- Green tint → buy signal regime active
- Red tint → sell signal regime active
- No tint → flat, no signal, regime neutral

This is the quickest visual cue. If bars are green-tinted AND the score is ≥ +6 AND M15 structure is bullish — conditions are stacking.

### Day 1 Exercise

On M5 XAUUSD, scroll through the last week of chart data. For each signal label:
1. Classify it: Sync / Local / Counter-Trend
2. Note the bar tint at signal time
3. Did the signal reach TP1? TP2? TP3?
4. What was the subsequent price behavior in the next 5-15 bars?

Build a frequency table: Sync signals → TP1 hit rate / Local signals → TP1 hit rate / CT signals → TP1 hit rate.

---

## DAY 2 — TUESDAY | CHART: THE FULL CONFLUENCE GATE (6-CHECK SYSTEM)

### The Gate — What It Is

Before executing any trade, the operator runs 6 checks. All 6 must pass. If any fails, the operator either investigates or SKIPs.

This is the operator's decision spine at the execution layer.

```
CONFLUENCE GATE — 6 CHECKS
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

CHECK 1: SCORE THRESHOLD
  □ Score ≥ +4 absolute (minimum to consider)
  □ Score ≥ +6 preferred for standard entry
  □ Score ≥ +8 for counter-trend entries
  HARD RULE: Score < ±4 = automatic SKIP. No exceptions.

CHECK 2: FRACTAL SYNC ALIGNMENT
  □ What is the current sync phase?
  □ FULLY ALIGNED = green light
  □ L3 or L4 PULLBACK = yellow (wait for flip or skip)
  □ SOVEREIGN NEUTRAL = red (no trade)
  RULE: FULLY ALIGNED or strong LOCAL signal (L4) only.

CHECK 3: V.WAP DIRECTION
  □ Signal is BUY → V.WAP swing must be BULL (lastSwing == 1)
  □ Signal is SELL → V.WAP swing must be BEAR (lastSwing == -1)
  □ V.WAP direction visible on dashboard: VWAP 📈 or 📉
  HARD RULE: VWAP against signal direction = SKIP or flag as CT.

CHECK 4: REGIME + FIB TREND ALIGNMENT AT M5
  □ Regime: is RSI bullish (>55 for buy) / bearish (<45 for sell)?
  □ Fib Trend: is M5 Fib Bull (for buy) / Bear (for sell)?
  □ Dashboard: ATM 🟢 Regime 📈 VWAP 📈 Fib 📈 (all green = ideal)
  RULE: At least Regime AND V.WAP must align. Fib misalignment = investigate.

CHECK 5: M15 STRUCTURE CONFIRMATION
  □ M15 BOS sequence confirms signal direction?
  □ Price in correct zone? (Discount for buy, Premium for sell)
  □ No BSL directly above a buy entry (unswept BSL = trap risk)
  □ No SSL directly below a sell entry (unswept SSL = trap risk)
  RULE: M15 structure must not conflict with signal direction.

CHECK 6: ATR CONTEXT + TIMING
  □ ATR: High or Med → proceed
  □ ATR: Low → require score ≥ +8, TP1 only target
  □ Are we inside an ICT killzone? (London: 10:00-13:00 EAT, NY: 15:00-18:00 EAT)
  □ Is it within 30 min of a major news event? → SKIP (news blackout period)
  RULE: Low ATR + outside killzone + low score = automatic SKIP.

━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
GATE VERDICT:
  All 6 pass → EXECUTE
  5 of 6 pass (ATR is the only failure) → CAUTIOUS EXECUTE (TP1 only)
  Any of checks 1-5 fail → SKIP or WAIT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

### Day 2 Exercise

Apply the confluence gate to 5 historical signals from the past week on M5.
For each signal: run all 6 checks. Determine EXECUTE, CAUTIOUS EXECUTE, or SKIP.
Then check the actual outcome. How accurate was the gate?

---

## DAY 3 — WEDNESDAY | SIGNAL: DASHBOARD 60-SECOND READ PROTOCOL

### The 60-Second Dashboard Read

The dashboard is the HUD. The operator reads it in sequence, never jumping sections.

```
STEP 1 (5 seconds): Score and sync phase
   Score: ___/15   Sync: _______________
   → Score ≥ +4? YES / NO
   → Fully Aligned? YES / NO

STEP 2 (10 seconds): Core signals
   ATM: 🟢/🔴/⚪   Regime: 📈/📉/⚪
   VWAP: 📈/📉/⚪  Fib: 📈/📉/⚪
   RSI: ___ MACD: Bull/Bear
   → Count how many align: __ of 4

STEP 3 (10 seconds): PDH/PDL context + Session
   Daily Context: Above PDH / Inside Range / Below PDL
   Session: London / NY / Asia
   ATR: High / Med / Low (___ pts/pips)
   → Is ATR sufficient? YES / NO

STEP 4 (15 seconds): 5-Layer Tree Narrative
   D: 🟢/🔴/⚪   H4: 🟢/🔴/⚪
   H1: 🟢/🔴/⚪  M15: 🟢/🔴/⚪  M5: 🟢/🔴/⚪
   → Are higher TFs (D, H4) aligned with lower TFs (M15, M5)?
   → Is any TF sharply conflicting (🔴 surrounded by 🟢)?

STEP 5 (10 seconds): Trade Setup (if active)
   Phase: _________  ID: ___________
   Entry: ___  SL: ___  TP1: ___  TP2: ___  TP3: ___
   Live P&L: ___
   → Is trade progressing toward TP1?
   → Has VWAP trail activated (Holder Mode)?

STEP 6 (10 seconds): Today's performance
   Sigs: __  Blocked: __  TP1: __  TP2: __  SL: __
   WR: __  PF: __  Net: __ pts
   → Am I ahead or behind for the day?
   → Is the profit factor above 1.5?

TOTAL: 60 seconds. Decision made.
```

### The MTF EMA Panel

Below the core signals, the dashboard shows the 9/21 EMA relationship across 9 timeframes:
```
 1m📈 5m📈 15m📈 30m📈 1H📈
 4H📈 1D📈 1W📈 1M📈
```
All pointing up = maximum momentum alignment.
Lower TFs pointing up but higher TFs pointing down = pullback in a larger downtrend.
This confirms or challenges the H1 intraday bias from the pre-session analysis.

### Day 3 Exercise

Set a timer. Open the agent dashboard on a live chart. Complete the 60-second read protocol. Write your notes. Make an EXECUTE / SKIP / WAIT decision.

Do this 5 times during the London session. Track: was your decision correct each time?

---

## DAY 4 — THURSDAY | RISK: THE TP PROTOCOL + TRADE MANAGEMENT

### How the Agent Manages the Trade

Once a signal is confirmed and the operator executes, the agent manages the trade through 4 phases:

**Phase 1: Active → Watching for TP1**
```
Entry locked. SL placed. Dashboard shows Phase: ACTIVE.
Live P&L tracking begins.
Operator does not interfere. Let the trade work.
```

**Phase 2: TP1 Hit (1:1 RR)**
```
Dashboard shows: TP1 ✅
Agent advice: Move SL to breakeven (or take half off if multi-lot)
Phase changes: TP1 HIT — TARGETING TP2
Operator action: Move SL to entry + 1-2 pts. This trade is now risk-free.
```

**Phase 3: TP2 Hit (1.5:1 RR)**
```
Dashboard shows: TP2 ✅
Phase changes: TP2 HIT — TARGETING TP3
Operator action: If running multiple lots, close a portion here.
                 Single-lot traders: trail SL behind M5 structure.
```

**Phase 4: TP3 Hit (2:1 RR) → Holder Mode**
```
Dashboard shows: TP3 ✅  Phase: 🔱 HOLDER MODE ACTIVE
VWAP trail takes over: SL trails behind the current VWAP value (vap_current)
Dashboard shows: 🔱 VWAP Trail: [price]
Operator action: Watch the trail. Exit when price closes below VWAP trail (long)
                 or above VWAP trail (short).
```

### The TP Levels — How They Are Calculated

```
SL distance = |entry - SL|  (in price units)

TP1 = entry + (SL distance × 1.0)   for longs
TP2 = entry + (SL distance × 1.5)   for longs
TP3 = entry + (SL distance × 2.0)   for longs

(Reversed for shorts)
```

On the dashboard:
```
 🎯 TP1: 2,346.50 (6.5 pts)
 🎯 TP2: 2,349.75 (9.75 pts)
 🚀 TP3: 2,353.00 (13.0 pts)
```

### Trade Management for Counter-Trend Signals

CT signals require tighter management:
- TP1 only target. Do not hold for TP2 or TP3.
- Move SL to breakeven immediately when TP1 is hit.
- Do not add to CT trades.
- If TP1 is not hit within 20-30 bars on M5, consider manual exit.

### Day 4 Exercise

For the last 5 completed trades (wins or losses, live or historical):
1. Did TP1 get hit? Did the operator move SL to breakeven after TP1?
2. Did TP2 get hit? What was the position management at that point?
3. Did any trade reach Holder Mode? How was the VWAP trail exit managed?
4. What was the actual RR achieved vs the expected TP3 target?

Build a table. Identify which phase of the TP protocol needs the most work.

---

## DAY 5 — FRIDAY | AUTOPSY: THE EXECUTION CARD — LIVE TRADE LOG

### The Execution Card

For every trade the operator takes, the execution card is filled in real time. See `/templates/EXECUTION_CARD.md`.

The execution card is the operator's live record. It serves as:
1. The pre-trade gate (must fill before executing)
2. The trade log (updated at each TP phase)
3. The post-trade autopsy source

### What a Clean Execution Looks Like

```
EXECUTION CARD — LIVE TRADE

Asset: XAUUSD       Date: 2026-06-28    Session: London
Signal ID: ATM-20260628-1032-L-1
Signal Type: SYNC BUY (4-Layer FULLY ALIGNED)
Score at signal: +9.0/15

PRE-TRADE GATE:
  □ Score ≥ +4: ✅ (9.0)
  □ Fractal sync: ✅ (FULLY ALIGNED)
  □ V.WAP direction: ✅ (BULL)
  □ Regime + Fib at M5: ✅ (both aligned)
  □ M15 structure: ✅ (bullish BOS, price in discount)
  □ ATR context: ✅ (High, 14.2 pts)
Gate verdict: EXECUTE

CAPITAL LOCK:
  Risk: $25.00
  Entry: 2,340.00
  SL: 2,333.50  (SL distance: 6.5 pts)
  TP1: 2,346.50  TP2: 2,349.75  TP3: 2,353.00
  Position size: 0.038 lots → run 0.03 lots
  Actual risk: $19.50

TRADE LOG:
  TP1 hit: YES  Time: 10:47 EAT  → SL moved to breakeven (2,340.00)
  TP2 hit: YES  Time: 11:12 EAT
  TP3 hit: NO   Price peaked at 2,352.40 (0.60 pts short of TP3)
  Exit: Manual at 2,350.80 (VWAP trail touched on M5 pullback)

RESULT:
  Exit price: 2,350.80
  Net points: +10.8 pts  Net P&L: +$32.40 (at 0.03 lots)
  RR achieved: 1.66:1
  Agent TP2 level: 1.5:1 → exceeded

AUTOPSY NOTE:
  Clean entry. Score was strong. Could have held to TP3 — price
  came within 0.60 pts. VWAP trail exit was correct discipline.
  No complaints.
```

### Day 5 Exercise

Take one trade today (or use a historical trade) and fill the complete execution card from memory — without looking at the template. Then compare to the template. What did you miss? What did you fill correctly?

This is your Friday close ritual. By Week 4, the execution card should take less than 5 minutes to fill pre-trade.

---

## WEEK 4 MASTERY CHECKLIST

Before advancing to Week 5, confirm all boxes:

- [ ] I can read the full dashboard in under 60 seconds
- [ ] I can run the 6-check confluence gate in under 3 minutes
- [ ] I can identify Master Sync vs Local vs Counter-Trend signals on sight
- [ ] I understand all 4 phases of the TP protocol (Active → TP1 → TP2 → Holder Mode)
- [ ] I have filled the execution card for at least 5 trades this week
- [ ] My EXECUTE/SKIP gate decisions matched correct outcomes in ≥ 65% of cases
- [ ] I have not taken a trade with a score below ±4 this week

**If any box is unchecked — repeat Week 4 before advancing.**

---

*Week 5 → Trade Management + Full System Integration*

*© Absolute Dollar Intelligence 2026 | Not financial advice.*
