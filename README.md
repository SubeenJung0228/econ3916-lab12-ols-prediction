# OLS as a Line of Best Fit: Estimation versus Prediction

## Objective

I fitted ordinary least squares regressions to the California Housing data to see how the same fitted model serves two different goals: estimating coefficients and predicting new rows.

## Methodology

- I fitted the same OLS line three ways (the closed-form solution, statsmodels and scikit-learn) and checked that all three gave the same intercept and slope.
- I added up to 100 columns of pure noise to a regression and tracked R-squared and adjusted R-squared as each set of noise columns went in.
- I split the data into training and test rows before fitting anything, then scored an eight-feature model on the rows it was fitted to and on test rows it never saw.
- I used one model specification, median income and rooms per household, for two jobs: reading off a coefficient with its 95% confidence interval, and measuring test RMSE.
- I wrote a small function, `holdout_rmse()`, that scores any list of predictors on the same test rows, and used it to score a model with median income alone.
- (Take-home Challenge) I dropped the districts sitting at the 5.0 ceiling from the training rows, refitted the model, and compared coefficients and test RMSE before and after.

## Key Findings

- **Three methods, one line.** The closed form, statsmodels and scikit-learn all gave the same intercept and slope. The slope was 0.4179.
- **R-squared rewards noise.** R-squared went from 0.4734 with no noise columns to 0.4758 with 100 noise columns, rising every time I added more. Adjusted R-squared did not keep rising: it went up slightly to 0.4735 at 5 noise columns, but by 100 it had fallen to 0.4732, below where it started. It charges a penalty for each added column, but it can still be fooled by a few useless ones.
- **Training error flatters the model.** The eight-feature model had a train RMSE of 0.7197 and a test RMSE of 0.7456. The gap is small, but the test number is the honest one.
- **One model, two answers.** The same specification (median income and AveRooms) gave an AveRooms coefficient of -0.0381 with a 95% CI of [-0.0430, -0.0333], and a test RMSE of 0.8379. The interval tells me about the estimate; the RMSE tells me about prediction. Neither one answers the other's question.
- **Fewer predictors, worse predictions.** Scored on the same test rows with `holdout_rmse()`, median income alone gave a test RMSE of 0.8421, compared with 0.7456 for the eight-feature model.

### Take-home Challenge: Dropping the Districts at the 5.0 Ceiling

- 992 districts (4.8%) sit at the 5.0 ceiling.
- Dropping them from the training rows barely moved the coefficients. MedInc went from 0.4487 to 0.4413, and the largest change was AveBedrms, from 0.7831 to 0.7179.
- Test RMSE fell slightly on all test rows (0.7456 to 0.7444) and on the uncensored test rows (0.6578 to 0.6486).
- Test RMSE rose on the ceiling districts (1.7876 to 1.8472), whose mean prediction fell from 3.847 to 3.733. Removing those districts from training pulled predictions for them further below the ceiling, so the model got slightly better on typical districts and worse on the ones it had stopped seeing.
