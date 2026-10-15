# Cairo Weather Forecast — Dataset and EDA Summary

## Region, period, and target

The dataset represents a point in Cairo, Egypt at latitude **30.0444** and longitude **31.2357**. Data was requested from **2021-01-01 through 2026-10-12**. The latest row with a valid temperature target is **2026-10-10** because NASA POWER returned a fill value for the requested cutoff date.

The target is `target_temperature_c`, which is NASA POWER `T2M`: daily average air temperature at 2 m in degrees Celsius. The final file contains **1,055 rows and 33 columns**. It includes 20 requested meteorological parameters plus calendar, cyclical, wind-component, and temperature-derived features. The data was downloaded daily and then sampled every two days so the full period would fit the requested **1,000–1,500 row** range.

## API choice

The project uses the free, public **NASA POWER Daily API**. It is suitable because it provides long historical daily time series for global coordinates without requiring a paid API key. The notebook documents the endpoint, coordinates, UTC time standard, yearly chunking, the 20-parameter request limit, and the parameter-to-column mapping.

## Main EDA findings

Cairo has a clear annual seasonal pattern. The mean target temperature is approximately **13.29 °C in January**, rises through spring, reaches approximately **30.90 °C in July**, and then falls through autumn. Across all usable rows, the target mean is **22.49 °C**, with a minimum of **6.57 °C** and a maximum of **35.01 °C**. Annual means were approximately 22.24 °C in 2021, 21.65 °C in 2022, 22.69 °C in 2023, 22.56 °C in 2024, 22.56 °C in 2025, and 23.46 °C in 2026; the last value is based on a partial year and should not be compared as a complete-year estimate.

A simple linear fit over the full series has an estimated slope of about **+0.493 °C per year**. This is a descriptive result, not a climate attribution: the period is relatively short and 2026 is incomplete. The notebook uses the 30-day rolling mean and rolling standard deviation to show the underlying trend and changing local variability without hiding short-term observations.

Because the raw target contains strong annual seasonality, it should be treated as **non-stationary in level for forecasting design** unless the formal ADF result in the notebook shows otherwise. The notebook runs ADF on the raw target, first differences, and annual seasonal differences. A forecasting model should use lagged target values and seasonal features or differencing, then validate chronologically.

## Data-quality decisions

The final target column has no missing values because the one requested date with an unavailable target was excluded rather than fabricated. The three radiation columns contain **36 missing values each** near the latest data window. These values are retained as missing and are reported by the notebook; they are not replaced with invented measurements. The notebook interpolates only short internal numeric gaps and reports the number of cells before and after interpolation. It also checks duplicate timestamps, the expected two-day interval, negative precipitation, humidity outside 0–100%, negative wind speeds, and cases where minimum temperature exceeds maximum temperature.

## Deliverables

- `cairo_weather_2021_2026_2day.csv`: the single cleaned tabular data file used by the analysis.
- `cairo_weather_eda.ipynb`: a reproducible notebook covering API acquisition, preparation, descriptive statistics, distributions, trend, seasonality, rolling variability, STL decomposition, ACF/PACF, ADF stationarity checks, correlations, anomaly detection, and an ML-ready chronological split.

## References

[1]: https://power.larc.nasa.gov/docs/services/api/temporal/daily/ "NASA POWER Daily API documentation"
[2]: https://power.larc.nasa.gov/docs/tutorials/parameters/ "NASA POWER Parameter Dictionary"
[3]: https://power.larc.nasa.gov/docs/services/api/ "NASA POWER API overview"
