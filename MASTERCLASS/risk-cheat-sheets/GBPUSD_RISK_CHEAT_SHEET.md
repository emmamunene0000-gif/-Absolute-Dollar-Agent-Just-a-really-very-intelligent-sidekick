# GBPUSD — RISK CHEAT SHEET
## Absolute Dollar Intelligence | Asset: British Pound / US Dollar
### Broker reference: Prop firm (SureLeverage / Atlas Funded)

---

## ASSET SPECS

| Spec | Value |
|------|-------|
| Symbol | GBPUSD |
| Asset type | Forex Major |
| Minimum lotsize | 0.01 (micro lot) |
| Contract notional | 100,000 GBP per standard lot |
| Pip value (0.01 lot) | **$1.00 per pip** (non-JPY forex standard) |
| 1 pip = | 0.0001 price movement |
| Typical spread | 0.3 – 1.2 pips (prop firm accounts) |
| Typical ATR (M5) | 5 – 15 pips (session dependent) |
| London session ATR | 8 – 18 pips |
| NY session ATR | 10 – 22 pips |
| Asia session ATR | 3 – 8 pips |

---

## DOLLAR RISK AT MINIMUM LOTSIZE (0.01 lot)

| SL Distance (pips) | Risk at 0.01 lot |
|--------------------|-----------------|
| 5 pips | $5.00 |
| 8 pips | $8.00 |
| 10 pips | $10.00 |
| 12 pips | $12.00 |
| 15 pips | $15.00 |
| 18 pips | $18.00 |
| 20 pips | $20.00 |
| 25 pips | $25.00 |

**Formula: Risk = SL_pips × 0.01 × 100,000 × 0.0001 = SL_pips × $1.00**

Note: Identical pip-dollar relationship to XAUUSD at 0.01 lot = $1/unit.

---

## POSITION SIZE TABLE (variable risk)

| SL (pips) | $10 risk | $15 risk | $20 risk | $25 risk | $30 risk | $50 risk |
|-----------|---------|---------|---------|---------|---------|---------|
| 5 pips | 0.02 | 0.03 | 0.04 | 0.05 | 0.06 | 0.10 |
| 8 pips | 0.013 | 0.019 | 0.025 | 0.031 | 0.038 | 0.063 |
| 10 pips | 0.01 | 0.015 | 0.02 | 0.025 | 0.03 | 0.05 |
| 12 pips | 0.008 | 0.013 | 0.017 | 0.021 | 0.025 | 0.042 |
| 15 pips | 0.007 | 0.01 | 0.013 | 0.017 | 0.02 | 0.033 |
| 20 pips | 0.005 | 0.008 | 0.01 | 0.013 | 0.015 | 0.025 |
| 25 pips | 0.004 | 0.006 | 0.008 | 0.01 | 0.012 | 0.02 |

---

## PROP FIRM CONTEXT — KEY RULES

GBPUSD on prop firm accounts (SureLeverage / Atlas Funded):

```
Daily drawdown limit: typically 5% of account
Max drawdown: typically 10% of account
Max position size: check firm-specific rules
News trading: most prop firms RESTRICT trading during major news
   → GBPUSD is affected by: BOE meetings, UK CPI, US NFP, FOMC

Daily risk cap recommendation (operator):
   Account $5,000 → daily risk cap $100 (2%)
   Account $10,000 → daily risk cap $200 (2%)
   Account $25,000 → daily risk cap $375 (1.5%)
```

**Always check prop firm rules before each session. Rule violations = funded account termination.**

---

## TP LEVEL REFERENCE

For a 12pip SL distance:
| Level | Distance |
|-------|----------|
| TP1 | 12 pips |
| TP2 | 18 pips |
| TP3 | 24 pips |

For a 15pip SL distance:
| Level | Distance |
|-------|----------|
| TP1 | 15 pips |
| TP2 | 22.5 pips |
| TP3 | 30 pips |

---

## ATR CONTEXT — GBPUSD

| ATR Level | Typical Range | Agent Shows | Operator Action |
|-----------|--------------|------------|-----------------|
| Low | < 5 pips M5 | ATR: Low | SKIP. GBPUSD ranging. |
| Medium | 5–10 pips M5 | ATR: Med | Standard. TP1→TP2. |
| High | > 10 pips M5 | ATR: High | Full protocol. TP3 achievable. |

---

## GBPUSD SESSION CHARACTERISTICS

**London Open (10:00 EAT / 07:00 UTC):**
Most important session for GBPUSD. Highest ATR. BOE-related flows dominate. Best signal quality.

**NY Open (15:00 EAT / 12:00 UTC):**
London-NY overlap. High volume. USD flows compete with GBP flows. Can create sharp reversals.
Watch for: USD news releases causing counter-trend moves.

**Asia (00:00 – 07:00 EAT):**
Low volume. GBPUSD barely moves. Do not trade GBPUSD in Asia except on high-impact UK early releases.

---

## COMMON GBPUSD MISTAKES

1. **Not checking the economic calendar** — GBPUSD is a news-sensitive pair. BOE meetings, UK CPI, and US NFP can cause 50-100+ pip moves in minutes. Close or protect positions before major news.

2. **Confusing pips and points** — The agent displays GBPUSD moves in PIPS. On the agent chart, 1 pip = 0.0001 price movement. A 15pt display = 15 pips.

3. **Ignoring the spread at prop firm** — Prop firms may have wider spreads during news. This eats into TP1. Factor in 1-2 pips of spread in your SL distance calculation.

4. **Treating GBPUSD like XAUUSD in terms of conviction** — GBPUSD requires stronger structural confirmation than Gold. Gold moves more predictably on momentum. GBPUSD is more susceptible to fake-outs at key levels.

---

*© Absolute Dollar Intelligence 2026 | Not financial advice. Verify with your broker and prop firm.*
