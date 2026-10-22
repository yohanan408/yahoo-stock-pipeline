# Yahoo Stock Returns: Time-Series Analysis and Model Benchmarking

An R project that cleans a daily stock-price series, studies how its returns and volatility behave, and tests whether four models can forecast next-day returns better than a naive baseline. **Short answer: they cannot, once look-ahead leakage in the features is removed.**

**Full report:** [yohanan408.github.io/yahoo-stock-pipeline](https://yohanan408.github.io/yahoo-stock-pipeline/)

---

## Key findings

- **Returns are negatively skewed** (skewness -1.14), so large single-day drops are more extreme than comparable rallies.
- **Volatility clusters.** The autocorrelation of absolute daily returns is 0.48 at lag 1 and stays above the 95% significance bound at all 40 lags tested.
- **The 2020 spike is clear.** The 20-day rolling volatility averaged 0.007 before 2020 and peaked at 0.0617 on 27 March 2020, about 8.8 times that average. The PELT change-point algorithm (MBIC penalty, 20-day minimum segment) found 7 breaks in the mean and variance of returns, two of them between 21 February and 6 April 2020.
- **No model reliably beats the naive baseline.** The baseline predicts a zero return every day. On a 30-day hold-out, Ridge and SVR are within noise of it, and Random Forest and LightGBM are worse.
- **A longer test agrees.** Over 20 consecutive 30-day windows, the best model (Ridge) beat the baseline in 13 windows, with a mean RMSE of 1.1685% against 1.1705%. A record that good happens by chance about 13% of the time when there is no real skill, so it is not evidence of forecasting ability.

### Results

30-day hold-out (RMSE and MAE in %):

| Model | RMSE | MAE |
|:--|--:|--:|
| Naive zero-return baseline | 1.2612 | 0.9966 |
| Ridge Regression | 1.2589 | 0.9976 |
| Support Vector Regression (RBF) | 1.2616 | 0.9919 |
| Random Forest | 1.2984 | 1.0347 |
| LightGBM | 1.3228 | 1.0392 |

Rolling-origin evaluation (20 windows of 30 days; naive mean RMSE 1.1705%):

| Model | Mean RMSE (%) | Windows beating the baseline |
|:--|--:|--:|
| Ridge | 1.1685 | 13 of 20 |
| Random Forest | 1.1759 | 11 of 20 |
| SVR | 1.1835 | 7 of 20 |
| LightGBM | 1.2265 | 6 of 20 |

### A leakage fix worth knowing about

An earlier version of this project computed the 5, 20 and 60-day rolling-volatility features without shifting them by one day, so each feature included the return it was trying to predict. LightGBM and SVR looked better than the baseline as a result. After shifting those features by one day, that advantage disappeared. The numbers above are the corrected ones. Details are in the report.

---

## Pipeline

1. **Data cleaning.** Chronological sort, weekend rows removed, and 8 outliers flagged by a 20-day rolling Z-score (|z| > 3) and clipped.
2. **Features.** Log returns, return lags 1 to 5, volume lags 1 and 2, daily-range lags 1 and 2, rolling volatility over 5, 20 and 60 days (each shifted by one day), and 14-day RSI (lagged by one day). About 1,225 rows remain for modelling.
3. **Exploratory analysis.** Return skewness, autocorrelation of absolute returns, rolling volatility, and change-point detection with PELT.
4. **Models.** Ridge regression, Random Forest, support vector regression (RBF kernel) and LightGBM, with fixed hyperparameters that were not tuned on the test data.
5. **Evaluation.** A chronological split with the last 30 trading days held out, min-max scaling fitted on training data only, and a rolling-origin evaluation across consecutive 30-day windows.

## Tech stack

R with `tidyverse`, `zoo`, `TTR`, `changepoint`, `glmnet`, `randomForest`, `e1071`, `lightgbm`, and `rmarkdown` / `knitr` for the report.

## Reproduce

1. Clone the repository and open `yahoo-stock-pipeline.Rproj` in RStudio.
2. Place the input data at `data/raw/yahoo_stock.csv`.
3. Install the packages once:
   ```r
   install.packages(c("tidyverse", "zoo", "TTR", "changepoint", "glmnet",
                      "randomForest", "e1071", "lightgbm", "rmarkdown"))
   ```
4. Open `index.Rmd` and click **Knit**. This writes `index.html`, which is the report served at the link above. A fixed random seed (`set.seed(42)`) makes the results repeatable.

## Repository contents

| File or folder | What it is |
|:--|:--|
| `index.Rmd` | The full analysis: code, results and write-up |
| `index.html` | The knitted report, served through GitHub Pages |
| `data/raw/` | Input data (`yahoo_stock.csv`) |
| `yahoo-stock-pipeline.Rproj` | RStudio project file |

## Limitations

- One price series from November 2015 to November 2020, which includes the 2020 crisis. Results may not hold for other periods or assets.
- A single 30-day hold-out and 20 rolling windows are small samples, so small differences are noise.
- The features are limited to lagged returns, volume, range, rolling volatility and RSI.
- No transaction costs or trading rules are modelled. This is a forecasting test, not a trading strategy.
- Interpretations such as loss aversion or panic are hypotheses. This analysis does not test them.

## Next steps

Volatility is persistent while returns are not predictable here. A more promising target is next-day volatility, compared against a naive baseline of yesterday's volatility, using the same lagged features.

## License

MIT. See [LICENSE](LICENSE).
