# WEEK 3 — M15 TACTICAL ANALYSIS + LIQUIDITY MAP
## The Battlefield Week | M15 is where the trade decision is made, not where it is executed

---

> **"M15 is the tactical intraday battlefield of price.
>  Here we are interested in liquidity — BSL, SSL, PDL, PDH, VAL, VAH, POC,
>  strong and weak swing — this is why the masterclass is important."**

---

## WEEK 3 MASTERY GATE

By end of Day 5 this week, the operator must be able to:
- Read the M15 chart as a liquidity map — identifying every buy-side and sell-side pool
- Mark PDH, PDL, VAH, VAL, POC from the volume profile on M15
- Identify the nearest BSL and SSL and understand what the agent expects price to do with them
- Understand ATR context and its role in filtering low-probability setups
- Filter ranging markets and low-ATR assets with confidence

---

## DAY 1 — MONDAY | CONCEPT: LIQUIDITY — THE AGENT'S PRIMARY PRICE DRIVER

### What Liquidity Is in This Context

Price does not move randomly. It moves to collect liquidity — the resting buy and sell orders above swing highs and below swing lows. The agent's liquidity engine tracks these pools.

```
BSL — Buy-Side Liquidity
   Resting stop-loss orders from short sellers sit ABOVE swing highs.
   When price sweeps above a swing high, it triggers those stop losses
   and fills them as market buy orders. Price often reverses after the sweep.
   Agent labels: BSL (red, above swing highs)

SSL — Sell-Side Liquidity
   Resting stop-loss orders from long buyers sit BELOW swing lows.
   When price sweeps below a swing low, it triggers those stop losses
   and fills them as market sell orders. Price often reverses after the sweep.
   Agent labels: SSL (teal, below swing lows)
```

### The Liquidity Dashboard Row

The dashboard shows the agent's real-time liquidity read:
```
 💧 LIQUIDITY
 💧 BREAKING SWING HIGH  ← price just swept BSL
 🟢 Buy-side:  2,358.40  ← nearest BSL level
 🔴 Sell-side: 2,321.60  ← nearest SSL level
```

Or when approaching without breaking:
```
 🧲 NEAR SWING HIGH  ← price is approaching BSL
```

### Why Liquidity Sweeps Matter for Execution

The agent does not just show where liquidity is — it uses liquidity sweeps as directional confirmation:

- Price sweeps BSL (takes out highs) → often reverses bearish → look for sell setups after sweep
- Price sweeps SSL (takes out lows) → often reverses bullish → look for buy setups after sweep
- Price approaching BSL with BULL bias → agent favors continuation through, not reversal

### Day 1 Exercise

On M15 XAUUSD (or your primary asset):
1. Mark the 3 most recent BSL pools (above recent swing highs)
2. Mark the 3 most recent SSL pools (below recent swing lows)
3. Did price sweep any of them in the last 24 hours?
4. What happened immediately after each sweep?

Write your observations. Are BSL sweeps followed by bearish structure? Are SSL sweeps followed by bullish structure?

---

## DAY 2 — TUESDAY | CHART: PDH, PDL, VAH, VAL, POC

### Previous Day High and Low (PDH / PDL)

Every day leaves behind a PDH (Previous Day High) and PDL (Previous Day Low). These are magnetic levels — price frequently returns to test them.

The agent's PDH/PDL Context Engine tracks this and displays:
```
 📅 Daily Context: Above PDH  → price broke yesterday's high (expansion bullish)
 📅 Daily Context: Below PDL  → price broke yesterday's low (expansion bearish)
 📅 Daily Context: Inside Daily Range → price contained between PDH and PDL
```

On M15, manually mark:
- PDH as a red horizontal line
- PDL as a green horizontal line

If price is Inside Daily Range, PDH is potential resistance and PDL is potential support.
If price is Above PDH, the broken PDH becomes support. Look for buys on retests.
If price is Below PDL, the broken PDL becomes resistance. Look for sells on retests.

### Volume Profile: VAH, VAL, POC

The agent's volume profile engine computes the session volume profile and displays three key levels:

```
POC — Point of Control
   The price level where the most volume traded during the session.
   Acts as a magnetic level — price is drawn to POC.
   Often the equilibrium of the session.
   Agent: red horizontal line on chart.

VAH — Value Area High
   The upper boundary of the value area (default: 70% of session volume).
   Price above VAH is "expensive" relative to where most trading occurred.
   Agent: aqua horizontal line on chart.

VAL — Value Area Low
   The lower boundary of the value area.
   Price below VAL is "cheap" relative to session value.
   Agent: aqua horizontal line on chart.
```

### Reading the Value Area on M15

When price is:
- Above VAH → in premium relative to session value → sells from VAH toward POC
- Between VAL and VAH → inside value → range plays, mean reversion to POC
- Below VAL → in discount relative to session value → buys from VAL toward POC

This aligns directly with the Premium/Discount/Equilibrium framework from Week 2.

### How H4 Closes Tell the Story

The daily candle is 1. On H1, there are 24 candles to fill the day.
On H4, there are 6 candles per day (Asia close → London open → NY open → NY close).

The operator watches H4 closes to understand how the day is developing:
```
Asia H4 close (around 07:00 UTC) → sets the London opening bias
London H4 close (around 11:00 UTC) → confirms London direction
NY open H4 close (around 15:00 UTC) → confirms if NY continues or reverses London
```

Combined with ICT killzones:
```
Asia Killzone:    00:00 - 04:00 UTC (03:00 - 07:00 EAT)
London Killzone:  07:00 - 10:00 UTC (10:00 - 13:00 EAT)
NY Killzone:      12:00 - 15:00 UTC (15:00 - 18:00 EAT)
```

The agent's current_session variable tracks this:
```
🗽 NY SESSION
👑 LONDON SESSION
🗼 TOKYO/ASIA
```

### Day 2 Exercise

On M15 for today's primary asset:
1. Mark PDH and PDL manually
2. Mark VAH, VAL, POC from the volume profile
3. Note where current price is relative to all 5 levels
4. Identify: which levels are potential support? Which are potential resistance?
5. What does the agent dashboard show for Daily Context?

---

## DAY 3 — WEDNESDAY | SIGNAL: M15 TACTICAL READ — THE FULL SEQUENCE

### The M15 Pre-Trade Checklist

Before the agent fires a signal on M5 or M1, the operator should already have the M15 map filled. The M15 checklist:

```
□ STRUCTURE
  □ Last M15 BOS direction: BULL / BEAR
  □ Any recent CHoCH on M15? YES / NO
  □ Strong High at: ___________
  □ Weak High at:   ___________
  □ Strong Low at:  ___________
  □ Weak Low at:    ___________

□ LIQUIDITY
  □ Nearest BSL at: ___________  (distance from current price: ___ pts)
  □ Nearest SSL at: ___________  (distance from current price: ___ pts)
  □ Price recently swept BSL? YES / NO  → When: ___________
  □ Price recently swept SSL? YES / NO  → When: ___________

□ VALUE AREA
  □ PDH: ___________
  □ PDL: ___________
  □ VAH: ___________
  □ VAL: ___________
  □ POC: ___________
  □ Current price position: Above VAH / Inside Value / Below VAL

□ BIAS DERIVED FROM M15
  □ Tactical bias: BULL / BEAR / NEUTRAL
  □ Key target for the session (where is price most likely going?): ___________
  □ Key level that invalidates bias: ___________
```

### The M15 Agent Read

When the agent fires on M5 and the operator has the M15 map, the evaluation is:

1. Does the M5 signal direction match the M15 tactical bias?
2. Is price in the correct zone for this signal? (Discount for longs, Premium for shorts)
3. Is the nearest BSL/SSL consistent with the signal direction? (SSL swept → buy is valid)
4. What is the score? Is it ≥ +4 absolute?

If all four align → EXECUTE.
If any one conflicts → INVESTIGATE before executing. Often SKIP.

### Day 3 Exercise

Fill the complete M15 pre-trade checklist for your primary asset at 05:30 EAT.
Then watch through the London session.
When the agent fires (or when a signal appears on M5):
- Evaluate against your checklist
- Make the EXECUTE or SKIP call
- Write the reason for your decision
- After 4 hours, review: was the decision correct?

---

## DAY 4 — THURSDAY | RISK: ATR CONTEXT AND THE RANGING MARKET FILTER

### What ATR Context Means

The agent computes ATR as High, Medium, or Low:
```
ATR = High  when current ATR > 1.20 × 20-period ATR average
ATR = Low   when current ATR < 0.80 × 20-period ATR average
ATR = Med   otherwise
```

Dashboard shows:
```
 NY SESSION  |  ATR: High (18.4)
```
or
```
 LONDON SESSION  |  ATR: Low (4.2)
```

### Why ATR Matters for Intraday Momentum Trading

ADI's core strategy is intraday momentum on M1 and M5. Momentum requires ATR. Low ATR = no momentum = no edge for this strategy.

```
ATR High → ideal condition. Price has range. TP1, TP2, TP3 are achievable.
ATR Med  → acceptable. Monitor TP3 carefully. Trail from TP1 if uncertain.
ATR Low  → caution. Score must be ≥ +8 to consider. TP1 only target.
           In Low ATR, even a technically valid signal may fail to reach TP1.
```

### The Score × ATR Matrix

```
          ATR: High    ATR: Med    ATR: Low
Score ≥10   AGGRESSIVE  CONFIDENT   CAUTIOUS (TP1 only)
Score ≥6    CONFIDENT   CONFIDENT   CAUTION
Score ≥4    CAUTION     WAIT        SKIP
Score <4    SKIP        SKIP        SKIP
```

### Ranging Markets — How to Identify and Filter

A ranging market on M15 shows:
- No clear BOS progression (no sequence of higher highs or lower lows)
- Multiple equal highs and equal lows (EQH/EQL labels from the SMC engine)
- POC is near the middle of the range (not near extremes)
- ATR is Low or falling
- Score bouncing between +2 and -2 (no sustained directional bias)

In a ranging market:
- ATM signals fire more frequently but have lower quality
- Many signals will be blocked by the regime filter (regime flips rapidly)
- The rejection count in TODAY section of dashboard will be high
- Operator action: WAIT. Take no trades until one side of the range breaks with volume.

### Day 4 Exercise

Look at your primary asset over the last 5 trading days on M15.
Identify: which days were trending days (clear BOS sequence)?
Which days were ranging days (no clear sequence, multiple EQH/EQL)?

For each ranging day: How many signals fired? How many reached TP1? What was the ATR level?
Write your conclusions about trading ranging conditions.

---

## DAY 5 — FRIDAY | AUTOPSY: LOW-PROBABILITY TRADE IDENTIFICATION

### The Five Signs of a Low-Probability Trade

Based on 4+ years of live market reading and the agent's autopsy data, these are the five patterns that consistently produce low-probability outcomes:

**1. Score below +4 absolute**
The timeframes are not aligned. The agent may fire a signal but the Glass Box will show low conviction. Skip.

**2. Signal against M15 structure**
A buy signal on M5 while M15 is printing bearish BOS sequence. The M15 structure is stronger than the M5 trigger.

**3. Signal at a liquidity wall without sweep**
Price is approaching a BSL (swing high) from below with a buy signal — but the BSL has not been swept. Price is likely to take that liquidity before any upside continuation.

**4. Low ATR + inside value area**
ATR is Low and price is between VAL and VAH. No edge. This is a ranging market mid-range. The agent's pip tracker will show this as TP1 misses across multiple recent signals.

**5. Counter-trend at low score**
CT-L or CT-S signal with score < ±6. Counter-trend trades require higher conviction, not lower. If the score is marginal and the sovereign layer is against you, this is a high-risk, low-quality setup.

### The Agent's Protection System

The agent already filters most of these through:
- Regime filter (blocks misaligned signals)
- Score threshold (Glass Box advice says "stand aside" below certain scores)
- Signal rejection reports (tells you exactly what was blocked and why)

The operator's job is to reinforce these filters with manual chart reading, not to override them.

### Day 5 Exercise

Review every signal from this week. For each signal (whether you traded it or not):
1. Was it a high-probability setup or a low-probability setup?
2. Which of the 5 signs applied (if any)?
3. If you traded a low-probability setup — what did you see that made you think it was valid?
4. What will you do differently next week?

---

## WEEK 3 MASTERY CHECKLIST

Before advancing to Week 4, confirm all boxes:

- [ ] I can identify BSL and SSL on M15 without the agent
- [ ] I can mark PDH, PDL, VAH, VAL, POC manually
- [ ] I can fill the complete M15 pre-trade checklist in under 10 minutes
- [ ] I understand ATR context and can identify a ranging market
- [ ] I can identify at least 3 of the 5 low-probability trade signs from this week's chart
- [ ] My EXECUTE/SKIP decision on signal day matched the correct outcome in ≥ 60% of cases

**If any box is unchecked — repeat Week 3 before advancing.**

---

*Week 4 → M5/M1 Execution + Dashboard Mastery*

*© Absolute Dollar Intelligence 2026 | Not financial advice.*
