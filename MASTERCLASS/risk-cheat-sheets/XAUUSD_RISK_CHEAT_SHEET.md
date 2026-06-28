# XAUUSD — RISK CHEAT SHEET
## Absolute Dollar Intelligence | Asset: Gold (XAUUSD)
### Broker reference: Pepperstone | Contract type: CFD on Spot Gold

---

## ASSET SPECS

| Spec | Value |
|------|-------|
| Symbol | XAUUSD |
| Asset type | Commodity — Precious Metal |
| Minimum lotsize | 0.01 (micro lot) |
| Contract notional | 100 oz per standard lot |
| Pip value (0.01 lot) | $0.01 per cent-move = **$1.00 per full point** |
| Typical spread | 0.2 – 0.8 pts (Pepperstone Raw) |
| Typical ATR (M5) | 6 – 20 pts (session dependent) |
| London session ATR | 10 – 18 pts |
| NY session ATR | 12 – 25 pts |
| Asia session ATR | 4 – 10 pts (low volatility) |

---

## DOLLAR RISK AT MINIMUM LOTSIZE (0.01 lot)

| SL Distance (pts) | Risk at 0.01 lot |
|-------------------|-----------------|
| 5 pts | $5.00 |
| 8 pts | $8.00 |
| 10 pts | $10.00 |
| 12 pts | $12.00 |
| 15 pts | $15.00 |
| 18 pts | $18.00 |
| 20 pts | $20.00 |
| 25 pts | $25.00 |

**Formula: Risk = SL_distance × 0.01 × 100 = SL_distance × $1.00**

---

## POSITION SIZE TABLE (variable risk)

Risk per trade → position size at given SL distance:

| SL (pts) | $10 risk | $15 risk | $20 risk | $25 risk | $30 risk | $50 risk |
|----------|---------|---------|---------|---------|---------|---------|
| 5 pts | 0.02 | 0.03 | 0.04 | 0.05 | 0.06 | 0.10 |
| 8 pts | 0.01 | 0.02 | 0.025 | 0.031 | 0.038 | 0.063 |
| 10 pts | 0.01 | 0.015 | 0.02 | 0.025 | 0.03 | 0.05 |
| 12 pts | 0.008 | 0.013 | 0.017 | 0.021 | 0.025 | 0.042 |
| 15 pts | 0.007 | 0.01 | 0.013 | 0.017 | 0.02 | 0.033 |
| 18 pts | 0.006 | 0.008 | 0.011 | 0.014 | 0.017 | 0.028 |
| 20 pts | 0.005 | 0.008 | 0.01 | 0.013 | 0.015 | 0.025 |
| 25 pts | 0.004 | 0.006 | 0.008 | 0.01 | 0.012 | 0.02 |

**Always round DOWN to nearest available lotsize. Never round up.**

---

## TP LEVEL REFERENCE (from entry price)

For a 10pt SL distance:
| Level | Distance | 1:1 reference |
|-------|----------|--------------|
| TP1 | 10 pts | 1:1 RR |
| TP2 | 15 pts | 1.5:1 RR |
| TP3 | 20 pts | 2:1 RR |
| Holder Mode | >20 pts | VWAP trail |

For a 15pt SL distance:
| Level | Distance |
|-------|----------|
| TP1 | 15 pts |
| TP2 | 22.5 pts |
| TP3 | 30 pts |

---

## ATR CONTEXT GUIDE — XAUUSD

| ATR Level | Typical Range | Agent Shows | Operator Action |
|-----------|--------------|------------|-----------------|
| Low | < 6 pts M5 | ATR: Low | SKIP unless score ≥ +8. TP1 only. |
| Medium | 6–12 pts M5 | ATR: Med | Standard execution. TP1→TP2 target. |
| High | > 12 pts M5 | ATR: High | Full TP protocol. TP3 achievable. |

---

## AGENT DASHBOARD REFERENCE — XAUUSD SPECIFIC

When the agent fires on XAUUSD, the dashboard will show:
```
💡 Min-Unit Risk: ~$[SL_distance] | Variable: $[risk_per_trade] → [size] Lots
```

The min-unit risk is always = SL_distance × $1.00 at 0.01 lot.
Use this to instantly verify the agent's risk calculation is correct.

---

## COMMON XAUUSD MISTAKES

1. **Treating XAUUSD like forex (pips)** — Gold moves in POINTS, not pips. A 10pt move on XAUUSD = 10 full price units (e.g., 2340 → 2350). At 0.01 lot, that is $10.

2. **Using a too-tight SL in London open** — London ATR can be 15+ pts. A 5pt SL will be hit by normal spread + volatility noise. Minimum SL buffer = 0.5 × ATR.

3. **Entering during Asia session** — Gold often consolidates in Asia. ATR is low. Wait for London open for quality setups.

4. **Holding through major news (NFP, CPI, FOMC)** — Gold is highly sensitive to economic news. Close positions or widen SL significantly in the 30 minutes around major releases.

---

*© Absolute Dollar Intelligence 2026 | Not financial advice. Verify with your broker.*
