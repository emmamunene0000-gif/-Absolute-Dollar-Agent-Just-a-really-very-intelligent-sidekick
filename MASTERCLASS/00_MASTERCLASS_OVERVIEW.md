# ABSOLUTE DOLLAR INTELLIGENCE — OPERATOR MASTERCLASS
## Framework Overview | ADSA v7.0 | Built from Live Markets

---

> **"We are no longer traders. We are operators. Agent traders."**
> **"The formula is: Analysis + Capital + Execution = Success."**

---

## WHAT THIS MASTERCLASS IS

This masterclass externalizes the intelligence inside the Absolute Dollar Agent (ADSA v7.0) so that every operator can understand *why* the agent fires, *how* to read what it is saying, and *when* to execute or skip.

The agent does the heavy lifting — it reads markets across 5 timeframes simultaneously, scores confluence from -15 to +15, fires Glass Box signal briefs, and manages the trade from entry through TP1 → TP2 → TP3 → Holder Mode. But the agent is a decision support system, not an autopilot. The operator's job is to evaluate the agent's output, confirm with manual chart reading, and execute with discipline.

This is not theory. Every concept in this masterclass is codified inside ADSA v7.0 and has been validated in live markets.

---

## THE TWO SYSTEMS — DO NOT CONFUSE THEM

**System 1: ADSA v7.0 (The Agent)**
- Pine Script v6 indicator running inside TradingView Pine Editor
- 17 subsystems: fractal sync, scoring engine, Glass Box narrative, SL autopsy, Telegram broadcast, risk model, trade progression, volume profile, SMC engine, VWAP, Fibonacci bands, liquidity swings, and more
- The agent signals. The operator evaluates and executes.

**System 2: The Agent Protocol Liquidity Suite (Access Tool)**
- The package operators receive when they onboard through ADI
- Includes the indicator file, setup call, pocket card, and this masterclass
- The suite is the vehicle. The agent is the intelligence.

---

## THE THREE POCKETS — OPERATOR DECISION SPINE

Every trade decision flows through three pockets in sequence. Never jump a pocket.

```
POCKET 1 — INTELLIGENCE
   Agent reads → Operator confirms
   Score ≥ 4 absolute to consider any trade
   Manual top-down analysis validates agent read

POCKET 2 — CAPITAL
   Absolute dollar risk first (never lotsize first)
   Risk Cheat Sheet: minimum lotsize per asset
   Position size = Risk ÷ (SL distance × Contract notional)

POCKET 3 — EXECUTION
   Entry model confirmed (M5/M1 trigger)
   TP protocol set before entry
   Counter-trend gate: EXECUTE or SKIP
   Final gate: does the chart confirm what the agent says?
```

---

## THE 4-LAYER TOP-DOWN CHAIN

The agent's fractal engine reads four layers simultaneously. The operator must walk this chain manually before executing.

```
L1 — SOVEREIGN (Daily / H4)
     The macro veto layer.
     Daily bearish = all longs flagged counter-trend (CT-L label on chart).
     H4 gives the directional leg for the trading session.

L2 — ANCHOR (H1)
     Intraday direction.
     Where is price relative to the current VWAP swing?
     What is the H1 structure doing? BOS or CHoCH?

L3 — FILTER / TACTICAL (M15)
     The intraday battlefield.
     Liquidity: BSL, SSL, PDH, PDL, VAH, VAL, POC.
     Strong and weak swing highs and lows.
     This timeframe tells you WHERE price is targeting.

L4 — EXECUTION (M5 / M1)
     The trigger layer.
     M5 for signal confirmation.
     M1 for precision entry on confirmed M5 setup.
     Core dashboard signals: ATM, Regime, V.WAP, Fib Trend.
```

---

## MASTERCLASS STRUCTURE

The masterclass is a **5-day weekly module**, repeatable for **3 to 5 weeks** depending on operator intelligence, discipline, and consistency.

| Week | Focus | Mastery Gate |
|------|-------|-------------|
| Week 1 | Risk Management + Agent Architecture | Operator can calculate correct lotsize for any asset from the risk cheat sheet |
| Week 2 | Top-Down Analysis + VWAP Swing Reading | Operator can fill the H1 analysis card manually before looking at the agent |
| Week 3 | M15 Tactical + Liquidity Map | Operator can identify BSL, SSL, PDH, PDL, VAH, VAL, POC and mark them before the agent fires |
| Week 4 | M5/M1 Execution + Dashboard Mastery | Operator can read the full dashboard in under 60 seconds and make a EXECUTE/SKIP decision |
| Week 5 | Trade Management + Autopsy Protocol | Operator understands every TP level, VWAP trail trigger, and SL autopsy output the agent generates |

---

## DAILY MODULE STRUCTURE (ALL 5 WEEKS)

Each day follows the same structure:

```
DAY 1 — MONDAY    CONCEPT DAY
   Teach one core concept.
   Explain how it is codified in the agent.
   Show what it looks like on the dashboard or chart.

DAY 2 — TUESDAY   CHART DAY
   Apply Monday's concept on a live or historical chart.
   Fill the manual analysis card for the day's asset.
   Compare operator read vs agent read.

DAY 3 — WEDNESDAY  SIGNAL DAY
   Watch the agent fire (or not fire) in real time.
   Evaluate the signal against the manual card.
   Practice the EXECUTE or SKIP decision.

DAY 4 — THURSDAY   RISK DAY
   Apply the risk cheat sheet to the week's trades.
   Calculate correct position size for every setup seen this week.
   Review actual vs expected pips from the performance table.

DAY 5 — FRIDAY     AUTOPSY DAY
   Review the week's signal log.
   Read every SL autopsy the agent generated.
   Identify the one thing to improve next week.
```

---

## ASSETS COVERED IN MASTERCLASS

The agent is designed to perform on all assets. The masterclass prioritizes the operator's personal watchlist. Core assets used in examples:

| Asset | Class | Min Lotsize | ATR Context |
|-------|-------|------------|-------------|
| XAUUSD | Commodity/Metals | 0.01 | High ATR — 8-15 pts typical M5 |
| GBPUSD | Forex Major | 0.01 | Medium ATR — 6-12 pips M5 |
| VOL75 | Deriv Synthetic | 0.001 | Constant 75% volatility index |

Risk cheat sheets for each asset are in `/risk-cheat-sheets/`.

---

## SCORE THRESHOLDS — OPERATOR HARD RULES

The composite score runs from -15 to +15 across 5 timeframes (D, H4, H1, M15, M5). Each timeframe scores -3 to +3 based on Regime + V.WAP + Fib Trend alignment.

```
Score ≥ +10 AND ATR = High   → SOVEREIGN HIGH MOMENTUM BULLISH — aggressive entry
Score ≥ +6                   → BULLISH BIAS (Strong) — confident entry
Score ≥ +3                   → BULLISH BIAS (Moderate) — cautious, wait for ≥ +6
Score +1 to +2               → QUIET BULL — WAIT — sit on hands
Score 0                      → NEUTRAL — stand aside, no edge
Score -1 to -2               → QUIET BEAR — WAIT
Score ≤ -3                   → BEARISH BIAS (Moderate) — cautious
Score ≤ -6                   → BEARISH BIAS (Strong) — confident short
Score ≤ -10 AND ATR = High   → SOVEREIGN HIGH MOMENTUM BEARISH — aggressive short

HARD RULE: Score absolute value below 4 = no trade regardless of chart.
```

---

## HOW TO USE THIS MASTERCLASS

1. Start at Week 1, Day 1. Do not skip ahead.
2. Each day is a 30-45 minute session.
3. The recommended live session schedule (EAT):
   - 05:30 EAT — Pre-session analysis (fill the card before markets move)
   - 10:00 EAT — London open (primary session)
   - 15:00 EAT — New York open (overlap opportunity)
4. Every concept you learn maps directly to a dashboard row or agent output.
5. When in doubt, the agent is right. When confident, verify the agent is right.

---

*© Absolute Dollar Intelligence 2026 | Not financial advice.*
*Built in Nairobi from 4+ years of live market experience.*
