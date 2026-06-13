# Fixed Income Tool

A lightweight Python tool for fixed income analytics. Given a bond's basic characteristics and current market price, it computes the **yield to maturity (YTM)**, **modified duration**, **convexity**, and their associated **price sensitivity estimates** — with exact time-to-maturity calculated from the current date.

---

## Features

- YTM solving via Newton-Raphson root-finding (SciPy)
- Exact time-to-maturity from today's date
- Macaulay and Modified Duration
- Convexity
- Linear (duration-only) and second-order (duration + convexity) price change approximations for a 100bps parallel shift

---

## Requirements

- Python 3.8+
- `numpy`
- `scipy`

Install dependencies:

```bash
pip install numpy scipy
```

---

## Usage

Run the script and follow the interactive prompts:

```bash
python engine.py
```

**Example session:**

```
--- Fixed Income Analytics: YTM Solving ---
Face value: 100
Coupon rate (%): 3.625
Compounding frequency (/year): 1
Maturity date (DD/MM/YYYY): 31/01/2035
Current market price (%): 97.68

Calculated YTM: 3.97%
Time to maturity: 8.63 years
Modified duration: 6.80
Price change for 100bps (1%) shift: -6.80%
Convexity: 56.42
Accurate price change for 100bps (1%) shift: -6.52%
```

---

## Inputs

| Parameter | Format | Example |
|---|---|---|
| Face value | Numeric | `100` |
| Coupon rate | Percentage | `3.625` |
| Compounding frequency | Integer (periods/year) | `1` (annual), `2` (semi-annual) |
| Maturity date | DD/MM/YYYY | `31/01/2035` |
| Current market price | Percentage of face value | `97.68` |

---

## Methodology

**YTM** is solved numerically as the discount rate $r$ satisfying:

$$P = \sum_{t=1}^{N} \frac{C}{(1 + r/f)^t} + \frac{F}{(1 + r/f)^N}$$

where $C$ is the periodic coupon, $F$ is the face value, $f$ is the compounding frequency, and $N$ is the number of periods.

**Modified Duration** is derived from Macaulay Duration:

$$D_{mod} = \frac{D_{mac}}{1 + r/f}$$

**Convexity** accounts for the curvature in the price-yield relationship:

$$\text{Convexity} = \frac{1}{P \cdot (1 + r/f)^2} \sum_{t=1}^{N} PV(CF_t) \cdot (t^2 + t)$$

**Price change approximation** for a yield shift $\Delta y$:

$$\frac{\Delta P}{P} \approx -D_{mod} \cdot \Delta y + \frac{1}{2} \cdot \text{Convexity} \cdot (\Delta y)^2$$

---

## Limitations

- Assumes bullet (non-callable) bonds with fixed, periodic coupons
- Day count convention uses exact calendar days / 365.25 (not ACT/ACT or 30/360)
- Coupon periods are integer-truncated from time to maturity, which may introduce minor rounding vs. Bloomberg or Reuters analytics
- No accrued interest handling (clean price input assumed)
