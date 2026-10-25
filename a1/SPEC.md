# `co2_analysis.py` — module specification (Assignment 1)

Give this file to the Antigravity agent. The module must implement **exactly** these five functions,
with these names and signatures, using only `numpy` and `pandas`. The Colab notebook imports them.

Data: NOAA GML monthly mean CO₂ at Mauna Loa, `https://gml.noaa.gov/webdata/ccgg/trends/co2/co2_mm_mlo.csv`
(comment lines start with `#`; columns `year, month, decimal date, average, deseasonalized, ndays, sdev, unc`).

---

### 1. `load_monthly(source: str) -> pandas.DataFrame`
Read the CSV from a URL or a local path, skipping `#` comment lines.
Return a DataFrame with the columns `year`, `month`, `t` (the `decimal date`), `co2` (the `average`) and
`co2_des` (the `deseasonalized`), sorted by `t`, with a fresh integer index.

### 2. `fit_poly(t, y, deg: int, t0: float = 2000.0) -> tuple[numpy.ndarray, float]`
Least-squares polynomial fit of `y` against `t - t0` (centring keeps the fit well conditioned).
Return `(coeffs, rms)`: the coefficients in `numpy.polyfit` order (highest power first) and the root-mean-square
of the residuals `y - fit`.

### 3. `jan1_growth(df) -> pandas.Series`
Annual growth rate as NOAA defines it. The trend value on 1 January of year *Y* is the mean of the
`co2_des` values of December *Y−1* and January *Y*. The growth rate of year *Y* is
`jan1(Y+1) - jan1(Y)` in ppm/yr. Return a Series indexed by year, for every year where both
1 January values exist.

### 4. `seasonal_cycle(df, coeffs, first_year: int, last_year: int, t0: float = 2000.0) -> pandas.Series`
Subtract the polynomial `coeffs` (evaluated at `t - t0`) from `co2`, keep the rows with
`first_year <= year <= last_year`, and average the residual by calendar month. Return 12 values indexed 1–12,
shifted so that their mean is zero (ppm).

### 5. `dominant_periods(resid, n: int = 3) -> list[float]`
Periodogram of a monthly-sampled residual array: subtract its mean, take `|rfft|²`, ignore the zero-frequency
term, and return the periods (in **months**) of the `n` strongest peaks, strongest first.

---

### Tests (`test_co2_analysis.py`, run with `python -m pytest -q`)
Use **synthetic data with known answers** only — no network access in the tests. At minimum:

- `fit_poly` recovers the coefficients of an exact quadratic (to 1e-8) and returns `rms ≈ 0`;
- `jan1_growth` on a straight line of slope 2 ppm/yr returns 2.0 for every year;
- `seasonal_cycle` recovers the peak-to-trough amplitude of a pure 12-month sine of amplitude 3 ppm (≈ 6 ppm);
- `dominant_periods` returns 12.0 first for a 12-month sine.

You must add **one more test yourself**, by hand, that the agent did not write.
