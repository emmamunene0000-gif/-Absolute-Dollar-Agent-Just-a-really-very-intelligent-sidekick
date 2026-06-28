# WEEK 1 — RISK MANAGEMENT + AGENT ARCHITECTURE
## The Foundation Week | Operators must master this before touching a live trade

---

> **"Pocket 2 is capital — absolute dollar risk first. Lotsize is the output, never the input."**

---

## WEEK 1 MASTERY GATE

By end of Day 5 this week, the operator must be able to:
- State their absolute dollar risk for any trade before calculating lotsize
- Use the risk cheat sheet to find the minimum risk per asset
- Understand every layer of the fractal 4-layer sync on the dashboard
- Read the composite score and know whether to consider or skip a signal
- Name all 17 subsystems of ADSA v7.0 and their function

---

## DAY 1 — MONDAY | CONCEPT: THE PLATINUM RISK MODEL

### Why Risk Comes Before Everything

Most traders think: "I want to trade 1 lot." The agent thinks: "The operator's dollar risk is $25. The SL distance is 18 pips. What lotsize produces exactly $25 at risk?"

That is the Platinum Risk Model. It is the only mathematically sound way to approach position sizing.

### The Formula

```
Position Size = Risk ($) ÷ (SL Distance × Contract Notional)

Where:
  Risk ($)             = your absolute dollar risk for this trade
  SL Distance          = entry price minus stop loss level (in price units)
  Contract Notional    = the dollar value of 1 full lot for that asset

Asset Contract Notionals:
  Forex (GBPUSD, EURUSD, etc.)  → 100,000 per standard lot
  XAUUSD (Gold)                  → 100 per standard lot (= $1 per pip per 0.01 lot)
  Crypto                         → 1 per coin
  Futures                        → syminfo.pointvalue
```

### Worked Example — XAUUSD

```
Scenario:
  Account size: $500
  Risk per trade: $25 (5% of account — aggressive but chosen by operator)
  Entry: 2,340.00
  SL:    2,333.50
  SL distance: 6.50 pts

Calculation:
  Contract notional for XAUUSD = 100
  Position size = $25 ÷ (6.50 × 100) = $25 ÷ 650 = 0.038 lots

Minimum unit check (from risk cheat sheet):
  Min lot for XAUUSD = 0.01
  Risk at 0.01 lot = 6.50 × 0.01 × 100 = $6.50

Operator runs 0.03 lots (rounds down to nearest safe size).
Actual dollar risk = 6.50 × 0.03 × 100 = $19.50
```

### What the Dashboard Shows You

On the agent dashboard under TRADE SETUP when a trade is active:
```
 💡 Min-Unit Risk: ~$6.50 | Variable: $25 → 0.0385 Lots
```
The agent computes this automatically. The operator's job is to verify this number against the risk cheat sheet BEFORE executing.

### Day 1 Exercise

Open a calculator. For each asset below, calculate the position size given:
- Risk: $20
- SL distance: as noted

```
GBPUSD  SL = 12 pips     → Min lot 0.01, Notional 100,000
XAUUSD  SL = 8 pts       → Min lot 0.01, Notional 100
VOL75   SL = 45 pts      → Min lot 0.001, Notional 1

Answers:
GBPUSD:  $20 ÷ (0.0012 × 100,000) = $20 ÷ 120 = 0.17 lots → run 0.16 lots
XAUUSD:  $20 ÷ (8 × 100) = $20 ÷ 800 = 0.025 lots → run 0.02 lots
VOL75:   $20 ÷ (45 × 1) = $20 ÷ 45 = 0.44 lots → run 0.44 lots
```

---

## DAY 2 — TUESDAY | CHART: THE AGENT ARCHITECTURE WALKTHROUGH

### The 17 Subsystems — What Each One Does

The operator does not need to read the code. The operator needs to understand what each subsystem produces in the dashboard or on the chart.

**[1] Fractal 4-Layer Consensus**
Reads L1 (Sovereign), L2 (Anchor), L3 (Filter), L4 (Exec) simultaneously.
Dashboard shows: `L1 Sovereign: BULL | L2 Anchor: BULL | L3 Filter: BEAR | L4 Exec: BULL`
Agent phase shows: `L3 PULLBACK — Wait for Filter Bull Flip`

**[2] Composite 5-Timeframe Scoring Engine (+15 to -15)**
Scores D, H4, H1, M15, M5. Each layer: Regime + V.WAP + Fib Trend = max ±3.
Total = max ±15. Score below ±4 absolute = no trade.
Dashboard shows: `Score: 8.0/15`

**[3] Glass Box Alert Architecture**
Every signal fires a rich reasoning report — not just "buy" or "sell."
The report explains why the signal fired, what the score was, what the agent advises.
Operator reads this in Telegram War Room alert.

**[4] Trade Progression Engine (TP1 → TP2 → TP3 → Holder Mode)**
When a trade goes live, the agent tracks it bar by bar.
TP1 = 1:1 RR (SL distance × 1.0)
TP2 = 1.5:1 RR (SL distance × 1.5)
TP3 = 2:1 RR (SL distance × 2.0)
Holder Mode = VWAP trail takes over when TP3 is hit.

**[5] SL Autopsy Engine**
When a stop loss is hit, the agent does not go silent.
It fires a structured loss narrative: what the score was, what regime said, what layer failed.
This is the operator's most important learning tool.

**[6] Signal Rejection Reports**
When the regime filter blocks a signal, the agent tells you WHY.
"RSI Regime bearish — buy signal suppressed."
Operator learns what the agent protects them from.

**[7] SMC Engine**
Paints: BOS, CHoCH, Order Blocks (Internal + Swing), Fair Value Gaps, Equal Highs/Lows, Premium/Discount/Equilibrium zones, liquidity swings (BSL/SSL).
This is the structural layer the operator reads manually on M15.

**[8] Adaptive VWAP + Volume Profile + Fibonacci Bands**
V.WAP swing = the most recent BULL or BEAR label on the chart.
Swing period: 100 on M1, 50 on M5.
The VWAP direction is a primary input to the regime filter.

**[9] Platinum Risk Model**
Covered on Day 1. Routes through asset-specific contract notionals.
Prevents the 10,000x leverage error that flat forex math produces on crypto.

**[10] Dual Telegram Broadcast**
Public channel: sanitized 3-line signal (direction + bias only, no levels).
War Room (Premium): full Glass Box report with entry, SL, TPs, score, narrative, commentary.

**[11] Super Admin Control Panel**
Bias override: AUTO, FORCE BULL, FORCE BEAR, SILENCE.
Asset context note: displayed on dashboard, appended to all alerts.
Operator commentary: personal analysis appended to War Room only.

**[12] Trade ID Engine**
Every signal gets a unique ID: `ATM-YYYYMMDD-HHMM-DIR-N`
Example: `ATM-20260628-1032-L-1`
This is how operators log and track performance.

**[13] Pip Tracker**
Tracks actual vs expected pips for TP1, TP2, TP3, SL — daily reset at midnight.
Dashboard shows: `WR: 66.7% | PF: 2.40 | Net: +18.4 pts`

**[14] Performance Table**
5-column table: Metric, Unit, Actual, Expected, Hits.
Shows daily profit factor and win rate with ELITE/STRONG/GOOD/MARGINAL ratings.

**[15] Daily Report Engine**
At the configured UTC hour (default 21:00 UTC), fires a full trade log to War Room.
Includes session breakdown (LDN/NY/ASIA signal count).

**[16] Live Dollar P&L Tracker**
While a trade is active, the dashboard shows real-time unrealised P&L in dollars and pips.
`💵 Live P&L: +$14.30 (+7.2 pts)`

**[17] PDH/PDL Context Engine + Tree Narrative**
Shows whether price is Above PDH, Below PDL, or Inside Daily Range.
Tree narrative: 5-layer structured read showing ├──/└── format for all timeframes.

### Day 2 Exercise

Open TradingView Pine Editor. Load the ADSA v7.0 script. Open on XAUUSD M5.
Walk the dashboard from top to bottom. Find:
1. The current score
2. The current L1-L4 sync phase
3. Whether price is above PDH, below PDL, or inside range
4. The current V.WAP direction (BULL or BEAR label on chart)
5. The live performance table (TP1/TP2/TP3 hit counts)

Write down what you see for each field. This is your first dashboard read.

---

## DAY 3 — WEDNESDAY | SIGNAL: THE REGIME FILTER EXPLAINED

### Why Signals Get Blocked

The ATM bot generates raw buy and sell signals constantly. The regime filter is the agent's quality gate. A signal only passes if:

```
Regime filter = ON (default)
AND
VWAP required = ON (default)
AND
RSI momentum aligns with signal direction:
  Buy signal  → RSI must be bullish (above pmom=55) AND VWAP swing must be BULL (lastSwing == 1)
  Sell signal → RSI must be bearish (below nmom=45) AND VWAP swing must be BEAR (lastSwing == -1)
```

When a signal fires but regime blocks it, the chart shows a grey X cross. This is a Rejected signal. The Glass Box report explains the rejection.

### What FULLY ALIGNED Means

When the dashboard shows:
```
🔥 FULLY ALIGNED: BULLISH (4-LAYER)
```
It means:
- L1 Sovereign = BULL
- L2 Anchor = BULL
- L3 Filter = BULL
- L4 Exec = BULL
AND a buy signal just confirmed on L4.

This is the agent's highest conviction state. Score will typically be ≥ +6.

### The Counter-Trend Signal (CT-L / CT-S)

A gold triangle on the chart labelled CT-L or CT-S means the signal fires against the Sovereign (Daily) direction.

```
CT-L = Buy signal confirmed but Daily is BEAR (counter-trend long)
CT-S = Sell signal confirmed but Daily is BULL (counter-trend short)
```

These are NOT automatic skips. They are flags. The operator evaluates:
- Is the score still ≥ +4 absolute?
- Is there a clear structural reason for a counter-trend move (PDL sweep, discount zone bounce)?
- Is the ATR high enough to support the trade?

Counter-trend signals require tighter management. TP1 is the target. Do not hold for TP3 on CT signals.

### Day 3 Exercise

On a historical chart (XAUUSD or GBPUSD M5), find three examples of:
1. A signal that passed the regime filter (green Buy or red Sell label)
2. A signal blocked by the regime filter (grey X cross)
3. A 4-layer FULLY ALIGNED moment (look at the sync phase at the time of signal)

For each, note: What was the score? What was the V.WAP direction? What did the Fib Trend show?

---

## DAY 4 — THURSDAY | RISK: THE RISK CHEAT SHEET IN PRACTICE

### The Cheat Sheet Logic

The risk cheat sheet answers one question per asset: "What is my dollar exposure at the minimum available lotsize?"

This is the anchor number. Every operator must know this before touching any trade.

```
XAUUSD (Gold):
  Min lot: 0.01 | Contract notional: 100 | Pip value at 0.01: $0.01/pip × 100 = $1 per pt
  If SL = 10 pts → Risk at 0.01 lot = $10.00
  If SL = 15 pts → Risk at 0.01 lot = $15.00

GBPUSD:
  Min lot: 0.01 | Contract notional: 100,000 | Pip value at 0.01: $1.00/pip
  If SL = 10 pips → Risk at 0.01 lot = $10.00
  If SL = 15 pips → Risk at 0.01 lot = $15.00

VOL75 (Deriv):
  Min lot: 0.001 | Contract notional: 1 | Pip value at 0.001: $0.001/pt
  If SL = 500 pts → Risk at 0.001 lot = $0.50
  At 0.10 lot   → Risk at 100 pt SL = $10.00
```

### The Dashboard Tells You This

When a trade fires, the dashboard shows:
```
💡 Min-Unit Risk: ~$6.50 | Variable: $25 → 0.0385 Lots
```

The min-unit risk is the cheat sheet number. The variable risk is what you set in the inputs. The lot size shown is what you should run.

### Day 4 Exercise

Using the risk cheat sheet templates (see `/risk-cheat-sheets/`):
1. For XAUUSD: given a $30 risk budget and a 12pt SL, calculate position size
2. For GBPUSD: given a $20 risk budget and a 14pip SL, calculate position size
3. For VOL75: given a $15 risk budget and a 75pt SL, calculate position size

Then verify: what would the agent compute for these same inputs?

---

## DAY 5 — FRIDAY | AUTOPSY: READING THE SL AUTOPSY ENGINE

### What the SL Autopsy Is

When the agent's stop loss is hit, it does not go silent. It fires a structured report into the War Room. This report is not punishment — it is intelligence.

The SL autopsy answers:
- What was the score when the signal fired?
- What did the Sovereign layer say? (Was this a counter-trend trade?)
- What was the ATR at time of entry? (Was the SL buffer adequate?)
- Which layer broke first?

### Reading an Autopsy

Example War Room SL autopsy output (reconstructed from agent logic):
```
━━━━━━━━━━━━━━━━━━━━━
🔴 SL HIT — TRADE CLOSED
━━━━━━━━━━━━━━━━━━━━━
ID: ATM-20260628-1032-L-1
Direction: LONG
Entry: 2,340.00 | SL: 2,333.50 | Hit: 2,333.48
Loss: -$19.50 (-6.5 pts)
Score at entry: +4.0/15
Sovereign at entry: BEAR (Counter-trend)
ATR at entry: Med (11.2 pts)
SL buffer used: 0.5 × ATR = 5.6 pts — TIGHT
Regime at SL: Bearish (regime flipped before TP1)
Agent diagnosis: Counter-trend long against Daily bear.
Regime flipped bearish before TP1. Score was marginal.
Lesson: Score ≥ +6 required for CT longs. At +4, risk was elevated.
```

### What the Operator Does With This

1. Log the trade in your weekly journal (see `/templates/WEEKLY_JOURNAL.md`)
2. Ask: did I follow the rules? Was the score ≥ +6? Was V.WAP aligned?
3. Identify the one thing that would have prevented this loss
4. Apply it next week

### Day 5 Exercise

Review any SL hits from this week (live or simulated). For each loss:
1. What was the score at time of signal?
2. Was it a FULLY ALIGNED signal or a counter-trend?
3. What did the dashboard show under CORE SIGNALS at entry time?
4. What would you do differently?

Write the answers in your journal. This is your Friday autopsy ritual.

---

## WEEK 1 MASTERY CHECKLIST

Before advancing to Week 2, confirm all boxes:

- [ ] I can calculate position size for XAUUSD, GBPUSD, and VOL75 without looking up the formula
- [ ] I know the minimum dollar risk at minimum lotsize for each of my watchlist assets
- [ ] I can name all 17 subsystems and their primary output
- [ ] I understand the difference between a FULLY ALIGNED signal and a COUNTER-TREND signal
- [ ] I have read at least one SL autopsy and written a journal entry
- [ ] I know what score threshold is required before I consider any trade

**If any box is unchecked — repeat Week 1 before advancing.**

---

*Week 2 → Top-Down Analysis + VWAP Swing Reading*

*© Absolute Dollar Intelligence 2026 | Not financial advice.*
