# WEEK 2 — TOP-DOWN ANALYSIS + VWAP SWING READING
## The Intelligence Week | How the Agent Reads the Map Before Every Trade

---

> **"First we conduct a manual multi-timeframe top-down analysis from H1 intraday.
>  The agent and the operator walk the same chain — in the same order."**

---

## WEEK 2 MASTERY GATE

By end of Day 5 this week, the operator must be able to:
- Conduct a full H1 → M15 → M5 top-down analysis manually
- Identify the current VWAP swing and its implied intraday play
- Mark Premium, Discount, and Equilibrium zones on H1
- Identify BOS and CHoCH on both H1 and M15
- Fill the pre-session analysis card from scratch, before consulting the agent

---

## PRE-SESSION TIMING — THE RECOMMENDED SCHEDULE (EAT)

```
05:30 EAT — PRE-SESSION ANALYSIS (before Asia close)
   Load H1 on your asset.
   Fill the analysis card: VWAP swing, P/D/E zones, structure, Fib bias.
   Read the agent dashboard: score, L1-L4 sync, PDH/PDL context.
   Set your session bias. Write it on the card.

10:00 EAT — LONDON OPEN
   Review the analysis card. Has the bias changed?
   Check: has price swept any liquidity from the pre-session read?
   ATM signals now active. Watch for M5 trigger.

12:00 EAT — MID-SESSION CHECK
   Has TP1 been hit? Has the regime flipped?
   M15 update — is structure still intact?
   Quick dashboard read: 60 seconds max.

15:00 EAT — NEW YORK OPEN
   NY-LDN overlap window. Highest velocity period.
   M5 trigger hunting. Score should be ≥ +6 for NY entries.

17:00 EAT — SESSION CLOSE
   Brief summary. P&L note. One autopsy if a loss occurred.
```

---

## DAY 1 — MONDAY | CONCEPT: THE VWAP SWING — YOUR INTRADAY COMPASS

### What the VWAP Swing Is

The Adaptive VWAP in ADSA v7.0 is not a fixed-session VWAP. It is a swing-anchored VWAP that resets every time price makes a new significant high or low.

The agent marks each new swing with a label on the chart:
- `BULL` label (below price, pointing up) = VWAP reset from a swing low. Bullish VWAP bias.
- `BEAR` label (above price, pointing down) = VWAP reset from a swing high. Bearish VWAP bias.

The current VWAP direction (`lastSwing`) is a primary input to the regime filter. A buy signal will be blocked if the VWAP swing is BEAR.

### Swing Period Settings

```
M5 chart → Swing Period = 50 (default)
M1 chart → Swing Period = 100

These are the recommended settings for intraday momentum trading.
A period of 50 on M5 means the VWAP looks back across 50 bars (250 minutes = ~4 hours)
to identify the most recent swing high or low from which to anchor.
```

### How to Read the VWAP Swing Manually

1. On H1, look at the most recent significant swing high and swing low.
2. Which one is more recent? That is the current VWAP anchor.
3. If the most recent significant move was from a low → bullish VWAP swing.
4. If the most recent significant move was from a high → bearish VWAP swing.

The V.WAP field in the dashboard's 5-layer narrative confirms this:
```
 ├── H1 🟢
 │    Regime :Long  V.WAP :Bull  Fib Trend :Bull  RSI:Bull
```

### The Intraday Play

The VWAP swing gives you the intraday play. If VWAP is:
- BULL on H1 → look for buy setups on M15/M5 during pullbacks to the VWAP line
- BEAR on H1 → look for sell setups on M15/M5 during rallies to the VWAP line

### Day 1 Exercise

On H1 XAUUSD, identify the last 3 VWAP swings (look for the BULL/BEAR labels).
For each swing, note:
1. Where was price anchored (swing high or swing low)?
2. How many bars did the VWAP swing last before it flipped?
3. What was the net directional move during each swing?

---

## DAY 2 — TUESDAY | CHART: PREMIUM, DISCOUNT & EQUILIBRIUM

### The Three Zones

The agent's SMC engine paints these zones automatically on the chart. The operator must understand what they mean before executing.

```
PREMIUM ZONE (above equilibrium)
   Price is expensive relative to the range.
   Sell-side opportunities. Shorts from premium into discount.
   Marked in RED on the agent chart.

EQUILIBRIUM (middle of range)
   The 50% level of the current price range.
   Fair value. Price often consolidates here.
   Marked in GREY on the agent chart.

DISCOUNT ZONE (below equilibrium)
   Price is cheap relative to the range.
   Buy-side opportunities. Longs from discount toward premium.
   Marked in GREEN on the agent chart.
```

### How the Agent Calculates These

The agent uses the `trailing` highs and lows engine (Section 18 of the code) to identify the current swing structure range. The midpoint of that range is Equilibrium. Upper half is Premium. Lower half is Discount.

### Reading Price Location in the Zones

When filling the analysis card (see templates), always record:
- Current price relative to zone: Premium / Equilibrium / Discount
- Is the agent's dashboard showing `Above PDH` or `Below PDL`? (PDH/PDL context engine)
- Is the score consistent with the zone? (Score ≥ +6 from Discount = high quality long setup)

### The PDH/PDL Context

Every dashboard reads:
```
 📅 Daily Context: Inside Daily Range
```
or
```
 📅 Daily Context: Above PDH
```
or
```
 📅 Daily Context: Below PDL
```

- Above PDH → price has broken above yesterday's high. Bullish expansion possible.
- Below PDL → price has broken below yesterday's low. Bearish expansion possible.
- Inside Range → contained between PDH and PDL. Look for reversals at PDH/PDL extremes.

### Day 2 Exercise

On H1 for your primary asset today:
1. Identify the current Premium/Discount/Equilibrium range visually (before looking at the agent zones)
2. Mark the PDH and PDL manually
3. Note where current price sits: Premium / Equilibrium / Discount
4. Note: Above PDH / Inside Range / Below PDL
5. Now open the agent dashboard and verify your manual read against the automated zones

---

## DAY 3 — WEDNESDAY | SIGNAL: STRUCTURE — BOS AND CHOCH

### Why Structure Is the Foundation

The SMC engine in ADSA v7.0 identifies two types of structural breaks:

**BOS — Break of Structure (Continuation)**
A BOS occurs when price breaks a previous significant high (in a bullish trend) or a previous significant low (in a bearish trend). It confirms the existing trend is continuing.

```
Bullish BOS: price breaks above a previous swing high → trend is still up
Bearish BOS: price breaks below a previous swing low → trend is still down
```

**CHoCH — Change of Character (Reversal Signal)**
A CHoCH occurs when price breaks against the prevailing trend structure. It is the agent's first warning that the trend may be changing.

```
Bullish CHoCH: price in a downtrend breaks above a previous swing high
Bearish CHoCH: price in an uptrend breaks below a previous swing low
```

### Internal vs Swing Structure

The agent shows two layers of structure:

**Internal Structure** (smaller, higher-frequency)
- Internal BOS and CHoCH fire on minor swings within the larger trend
- These are the M5/M1 signals — triggers for entry
- Displayed in smaller label size (Tiny)

**Swing Structure** (larger, lower-frequency)
- Swing BOS and CHoCH fire on major swing points
- These are the M15/H1 signals — confirm the intraday bias
- Displayed in larger label size (Small)

### Strong vs Weak Highs and Lows

The agent labels certain swing highs and lows as Strong or Weak:

```
Strong High: a swing high that was followed by a significant bearish BOS below it
   — price is likely to return and take out this high (liquidity above)

Weak High: a swing high in the current trend direction
   — not yet confirmed as a liquidity target

Strong Low: a swing low followed by a significant bullish BOS above it
   — price is likely to return and take out this low (liquidity below)

Weak Low: a swing low in the current trend direction
```

On the analysis card, operators mark: the most recent Strong High, Weak High, Strong Low, Weak Low on H1 and M15.

### Day 3 Exercise

On H1 XAUUSD (or your primary asset):
1. Identify the last 3 BOS events (bullish or bearish). Mark them.
2. Identify any CHoCH events in the last 48 hours. Mark them.
3. Label the current Strong High, Weak High, Strong Low, Weak Low.
4. Based on structure alone — what is the directional bias?
5. Does this match what the agent's L2 (Anchor H1) layer shows on the dashboard?

---

## DAY 4 — THURSDAY | RISK: FIBONACCI TREND AND HOW IT SCORES

### What Fib Trend Is in the Agent

The `trend_fib` variable in ADSA v7.0 is a Fibonacci bands-based trend direction.

The Fibonacci bands use a 21-period basis with ATR-based band expansion (14-period ATR). When close is above the basis line, `trend_fib = 1` (bullish). When below, `trend_fib = -1` (bearish).

In the dashboard's 5-layer narrative, each timeframe shows `Fib Trend :Bull` or `Fib Trend :Bear`.

### How Fib Trend Contributes to the Score

Each timeframe (D, H4, H1, M15, M5) scores Regime + V.WAP + Fib Trend.

```
Fib Trend Bull at that timeframe → +1 to score
Fib Trend Bear at that timeframe → -1 to score
Fib Trend neutral (at basis) → 0 to score
```

Five timeframes × up to +1 each = up to +5 from Fib Trend alone.

If V.WAP and Regime also align across all five timeframes → maximum score of +15.

### How to Read Fib Trend Manually

Without the agent: look at where price is relative to the 21-period midline on the chart. Is price clearly above? Fib Bull. Clearly below? Fib Bear.

With the agent: read the tree narrative row for each timeframe.

### Day 4 Risk Integration

Given the following dashboard tree narrative, calculate the total score manually:
```
D  (Sovereign)  — Regime: Long  V.WAP: Bull  Fib: Bull  → +3
H4 (Anchor)     — Regime: Long  V.WAP: Bull  Fib: Bear  → +1
H1              — Regime: Long  V.WAP: Bear  Fib: Bull  → +1
M15             — Regime: Neut  V.WAP: Bear  Fib: Bear  → -2
M5              — Regime: Short V.WAP: Bear  Fib: Bear  → -3

Total score = +3 +1 +1 -2 -3 = 0
```
Answer: Score = 0. Agent says NEUTRAL — STAND ASIDE. No trade.

### The Risk of Trading at Fib Conflict

When higher timeframes show Bull Fib but lower timeframes show Bear Fib (or vice versa), the score will be low. This is confluence conflict. It is the agent's way of saying: "the timeframes do not agree." Low score = no edge. No trade.

---

## DAY 5 — FRIDAY | AUTOPSY: THE PRE-SESSION CARD REVIEW

### Filling the Pre-Session Analysis Card

This is the operator's most important daily ritual. Complete it before the London open, at 05:30 EAT.

See the template at `/templates/PRE_SESSION_CARD.md`.

The card has two sections:
1. Manual chart read (filled by operator)
2. Agent dashboard confirmation (filled from the dashboard)

If the manual read and the agent dashboard conflict — investigate before trading. Never ignore the conflict.

### Day 5 Exercise

Fill the pre-session card for your primary asset using today's H1 chart.
Then open the agent and fill the confirmation section.

For each field where your manual read differed from the agent:
- Note the difference
- Determine which was correct (look at what price did next)
- Write one sentence explaining why they differed

This is how you calibrate your own eye to match the agent's intelligence over time.

---

## WEEK 2 MASTERY CHECKLIST

Before advancing to Week 3, confirm all boxes:

- [ ] I can identify the current VWAP swing direction on any chart without the agent
- [ ] I can mark Premium, Discount, and Equilibrium zones manually
- [ ] I can identify BOS and CHoCH on H1 and M15
- [ ] I can label Strong and Weak swing highs and lows
- [ ] I have filled the pre-session analysis card at least 3 times this week
- [ ] I can calculate the composite score manually from a given tree narrative
- [ ] My manual bias has matched the agent's bias on at least 4 out of 5 trading days

**If any box is unchecked — repeat Week 2 before advancing.**

---

*Week 3 → M15 Tactical Analysis + Liquidity Map*

*© Absolute Dollar Intelligence 2026 | Not financial advice.*
