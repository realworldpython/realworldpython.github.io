## Statistical Tests (7)

| Short Description | Function |
|---|---|
| Kolmogorov-Smirnov test of two distributions  | `stat.test.kolmogorov` |
| Compute residual of a fit | `stat.test.residual` | 
| Compute the pull of a fit  | `stat.test.pull` |
| Compute the Mahalanobis of a fit  | `stat.test.mahalanobis` |
| Update a cumulative χ2 calculation  | `stat.test.updateChi2` |
| Compute the reduced χ2  | `stat.test.reducedChi2` |
| Compute the χ2 probability | `stat.test.chi2Prob` |

## Sample Statistics (12)

| Short Description | Function |
|---|---|
| Incremental count, mean, and/or variance  | `stat.sample.update` |  
| Incremental covariance matrix  | `stat.sample.updateCovariance` | 
| Incremental count, mean, and/or variance in a window  | `stat.sample.updateWindow` |
| Exponentially weighted moving average (EWMA)  | `stat.sample.updateEWMA` | 
| Doubly exponential average with trend  | `stat.sample.updateHoltWinters` | 
| Triply exponential average: trend and period  | `stat.sample.updateHoltWintersPeriodic` |
| Make one forecast from a Holt-Winters state  | `stat.sample.forecast1HoltWinters` |
| Make many forecasts from a Holt-Winters state  | `stat.sample.forecastHoltWinters` |
| Fill a histogram | `stat.sample.fillHistogram` |
| Fill a two-dimensional histogram  | `stat.sample.fillHistogram2d` |
| Fill a counter/categorical histogram  | `stat.sample.fillCounter` | 
| Maintain a top-N list | `stat.sample.topN` |

## Change Detection (3)

| Short Description | Function |
|---|---|
| Historical record of triggered events  | `stat.change.updateTrigger` |
| Simple difference over uncertainty  | `stat.change.zValue` | 
| Cumulative sum | `stat.change.updateCUSUM` |

