# VOL 75 — RISK CHEAT SHEET
## Absolute Dollar Intelligence | Asset: Volatility 75 Index (Synthetic)
### Broker reference: Deriv

---

## ASSET SPECS

| Spec | Value |
|------|-------|
| Symbol | VOL75 (Volatility 75 Index) |
| Asset type | Deriv Synthetic Index |
| What it is | Simulated market with constant 75% volatility |
| Minimum lotsize | 0.001 |
| Contract notional | 1 (per unit, in USD) |
| Pip value (0.001 lot) | $0.001 per point |
| Typical ATR (M5) | 50 – 200 pts (highly variable) |
| Trading hours | 24/7 (synthetic — never closes) |
| Spread | ~0.5 – 2 pts typical on Deriv |

---

## DOLLAR RISK AT MINIMUM LOTSIZE (0.001 lot)

| SL Distance (pts) | Risk at 0.001 lot |
|-------------------|------------------|
| 50 pts | $0.05 |
| 100 pts | $0.10 |
| 200 pts | $0.20 |
| 300 pts | $0.30 |
| 500 pts | $0.50 |
| 1,000 pts | $1.00 |
| 2,000 pts | $2.00 |
| 5,000 pts | $5.00 |

**Formula: Risk = SL_distance × lotsize × 1 = SL_distance × lotsize**

---

## POSITION SIZE TABLE (variable risk)

| SL (pts) | $5 risk | $10 risk | $15 risk | $20 risk | $25 risk |
|----------|--------|---------|---------|---------|---------|
| 100 pts | 0.05 | 0.10 | 0.15 | 0.20 | 0.25 |
| 200 pts | 0.025 | 0.05 | 0.075 | 0.10 | 0.125 |
| 300 pts | 0.017 | 0.033 | 0.05 | 0.067 | 0.083 |
| 500 pts | 0.01 | 0.02 | 0.03 | 0.04 | 0.05 |
| 750 pts | 0.007 | 0.013 | 0.02 | 0.027 | 0.033 |
| 1,000 pts | 0.005 | 0.01 | 0.015 | 0.02 | 0.025 |
| 1,500 pts | 0.003 | 0.007 | 0.01 | 0.013 | 0.017 |
| 2,000 pts | 0.003 | 0.005 | 0.008 | 0.01 | 0.013 |

---

## IMPORTANT: VOL75 PRICE SCALE

VOL75 does not move in traditional pips or points. Its price scale is in index units, and moves can be large in absolute terms but small in dollar terms at minimum lots.

```
Example:
  VOL75 price: 456,230.50
  Signal fires at: 456,230.50
  SL at: 455,730.50 (500 pts below)
  TP1 at: 456,730.50 (500 pts above, 1:1)
  TP2 at: 457,480.50 (750 pts above, 1.5:1)
  TP3 at: 457,230.50 (1,000 pts above, 2:1)

  At 0.02 lot, $10 risk, 500pt SL:
    Risk = 500 × 0.02 = $10.00 ✅
    TP1 value = 500 × 0.02 = $10.00
    TP2 value = 750 × 0.02 = $15.00
    TP3 value = 1,000 × 0.02 = $20.00
```

---

## ATR CONTEXT — VOL75

Because VOL75 is a 75% volatility synthetic, its ATR is always relatively high. The distinction:

| ATR Level | Typical M5 Range | Agent Shows | Operator Action |
|-----------|-----------------|------------|-----------------|
| Low (for VOL75) | < 50 pts M5 | ATR: Low | Rare. Tighten SL. TP1 only. |
| Medium | 50 – 150 pts M5 | ATR: Med | Standard execution. |
| High | > 150 pts M5 | ATR: High | Full TP protocol. Wider SL. |

---

## VOL75 UNIQUE CHARACTERISTICS

**24/7 Trading:**
VOL75 never closes. This is both an advantage and a risk. There are no session-specific setups — the operator can theoretically trade at any time. However, the agent's London and NY killzone framework still applies as a discipline tool — restrict trading to killzone windows only.

**No External Macro News Impact:**
VOL75 is synthetic. It does not react to BOE meetings, NFP, FOMC, or geopolitical events. This removes news risk but also removes the macro confluence that strengthens real market setups.

**The Agent on VOL75:**
Because there is no volume data on synthetic indices, the volume profile engine (VAH/VAL/POC) operates on simulated volume. The SMC engine (BOS/CHoCH/OB) and VWAP engine work fully. The score is reliable. Liquidity sweeps (BSL/SSL) are particularly effective on VOL75 because the synthetic price algorithm creates highly repetitive sweep patterns.

**Compounding Advantage:**
The small notional per lot ($1) means operators can compound from micro accounts ($25-$100 Deriv deposits) using the full risk model. This is the learning account use case.

---

## DERIV ACCOUNT TIERS — VOL75

| Account tier | Min deposit | Recommended starting lot | Max daily risk |
|-------------|------------|------------------------|---------------|
| Micro | $25 | 0.001 | $2.50 (10%) |
| Small | $50 | 0.001–0.003 | $5.00 (10%) |
| Standard | $200 | 0.005–0.02 | $10.00 (5%) |
| Growth | $500 | 0.01–0.05 | $25.00 (5%) |

Start at the tier matched to your account. Do not run larger lots than the table suggests until the account is 2× the starting deposit.

---

## COMMON VOL75 MISTAKES

1. **Oversizing due to "it's cheap"** — The small pip value tricks operators into running too large. At 0.10 lot with a 1,000pt SL: risk = $100. On a $200 account that is 50% risk. Never.

2. **Ignoring the spread** — VOL75 spread can be 1-3 pts. On a 50pt SL, that is 2-6% of your SL eaten by spread. Factor spread into SL distance calculation.

3. **Trading 24/7 without a session filter** — Just because VOL75 is open 24/7 does not mean every hour is tradeable. Apply the same killzone discipline as you would to forex. Restrict to London and NY windows.

4. **Expecting smoother price action than real markets** — VOL75 is engineered with 75% volatility. This means very sharp, fast moves. M1 candles can be large. SL buffers must account for this.

---

*© Absolute Dollar Intelligence 2026 | Not financial advice. Verify with Deriv platform specs.*
