# IFRS 17 Risk Adjustment: Cost of Capital Calibration

A worked calibration of the IFRS 17 risk adjustment (RA) using the cost of capital method, on a synthetic term assurance and immediate annuity book. It compares the textbook (full) method with the factor method insurers use in practice, and tests when the factor shortcut is safe.

> All data is synthetic and illustrative: a 6% cost of capital, Solvency II-style stresses and a made-up portfolio. Nothing here is any company's calibration.

## Key findings

| Method | RA | Error vs full method | Implied confidence level (run-off view) |
| --- | --- | --- | --- |
| Full (textbook) method | €3.90m | — | 77.1% |
| One flat factor, calibrated at t = 0 | €2.70m | −31% | 69.6% |
| Age-banded factors (diversified) | €4.05m | +3.7% | 77.9% |

**Out-of-sample test** (bands frozen on an age-65 annuity book, applied to older books; longevity RA error):

| Annuity start age | Diversified (Euler) bands | Standalone bands |
| --- | --- | --- |
| 65 (in-sample) | +0.4% | +0.3% |
| 70 | +10.6% | −0.3% |
| 75 | +27.2% | −0.2% |

**Why:** the diversified factors were labelled by age but had also absorbed *when the term book runs off*. Standalone factors depend only on age, so they transfer. **Design conclusion:** calibrate standalone factors by band, then apply diversification each year, outside the factors.

## Method

RA = Σₜ CoC × Capital(t) × discount factor (IFRS 17 para 37, B86–B92), calculated backwards as RA(t) = CoC × C(t) × v + v × RA(t+1).

- **Full method:** per-policy BEL by backward recursion on best-estimate and stressed bases (mortality +15%, longevity −20%, lapse ±50%, expense +10%), with stresses re-run at every future year.
- **Diversification:** C = √(Σᵢ Cᵢ × Σⱼ ρᵢⱼ Cⱼ) with Solvency II Art. 136 correlations; Euler allocation to groups.
- **Factor method:** capital = factor × exposure (sum assured, PV of claims, PV of annuity payments, PV of profits, PV of expenses).
- **Confidence level (para 119):** normal approximation, with σ taken from the full capital path; the run-off view adds yearly shocks over the whole run-off.

## Repository contents

| File | What it is |
| --- | --- |
| `IFRS17_RA_Cost_of_Capital_Workbook.xlsx` | The workbook: inputs, projections, capital, RA, factor method, age bands (diversified and standalone band factors), confidence levels for the full and factor methods, and checks |
| `audit_trail.pdf` | Audit trail and summary: every step, what changed, results, diagnosis, assumptions and next steps |

The out-of-sample results above come from a separate run with the age-65 band factors frozen and the annuity starting age changed. In the workbook the band factors recalculate when the inputs change, so freezing them is the first step to reproduce the test (see the audit trail). The Python build will include this test.

## How to use the workbook

1. Start with the **Guide** sheet; blue cells are inputs, yellow cells are key assumptions.
2. Change inputs on **Inputs** (e.g. cost of capital 6% → 4.75%, or the annuity starting age) and follow the effect through **Capital**, **RA** and **Summary**.
3. Checks on **Summary** should all show 0.

## Limitations

- Synthetic data, one model point per group; Solvency II-style stresses stand in for a 1-in-200 capital model.
- Flat 3% discount rate; expense stress is level only (no inflation stress yet).
- Confidence level uses a normal approximation; a Monte Carlo simulation would replace it.
- Term-book factors (mortality, lapse) are not yet banded or tested out of sample.

## How this was built

The reference workbook was built with AI assistance as a learning scaffold. I added the age-band calibration (diversified and standalone band factors) and the factor-method confidence levels myself. The Python version will be my own build, checked against the workbook.

## Python build (in progress)

The Python version reproduces the Excel workbook first, then adds the corrected factor method (standalone factors by band, with diversification applied each year). Planned modules: `inputs.py`, `projection.py`, `capital.py`, `ra.py`, `factors.py`, `confidence.py`, and `tests/` with the Excel benchmarks as fixed checks.

## Next steps

- Python build reproducing every Excel benchmark
- Standalone factors for all risks with diversification applied each year
- Out-of-sample tests on the term book (different starting ages)
- Monte Carlo confidence level
- Running the model on the 12,000-policy book from my [Solvency II SCR calculator](https://github.com/klister98/solvency2-scr-calculator)
