# Frankel and Romer (AER, 1999) IV Monte Carlo Simulation

This repository simulates bilateral trade data
to estimate the effect of trade on output using the
[Frankel and Romer (AER, 1999)](https://www.aeaweb.org/articles?id=10.1257/aer.89.3.379)
predicted trade instrument.
I owe a great deal of my knowledge on this issue to Jonathan Dingel.

The equations estimated can be summarized in a two-stage least squares fashion,
with an additional "zero stage" gravity regression in order to obtain predicted
total log trade from predicted bilateral flows.
The equation we would like to estimate is the output equation that recovers
the effect of trade `Ti` on output `Yi` according to
```
Yi = α + β Ti + εi
```
However, trade and output are endogenous.
In order to estimate the effect of total trade on output,
we require an instrument `Z` for `Ti`.

Frankel and Romer (1999) propose using exogenous factors,
such as distance, language, contiguity, and other variables,
which exogenously determine the level of total bilateral trade,
but are unrelated to output except through the effect on trade.

Precisely, there is correlation between the residuals of the output equation
and the gravity equation governing bilateral trade
```
T_ij = Z_ij γ + η_ij
```
where the error term `η_ij` is correlated with the error from the output equation
according to the convolution formula
```
η_ij = ρ εi + √(1-ρ²)μi
```
where `μi` is an auxiliary standard Normal (0,1) random variable.
Note in passing that this the standard bivariate normal transformation to obtain
an error term with variance fixed to one `V(η_ij) = 1` (i.e. invariant to `ρ`)
with correlation equal to rho `Corr(η_ij,εi) = ρ`.
This is the "zero-th stage" gravity regression.
It uses "distance elasticties" to compute the predicted bilateral trade
driven by exogenous geographic factors.
Predicted values of bilateral log trade are transformed to obtain total log trade
according to
```
Tihat = LogSumExp(Z_ij * γ_hat)
```
The first stage estimating equation is a regression of observed total trade on
predicted total trade from the bilateral gravity equation
```
Ti = π_0 + Tihat π_1 + ν
```
This allows for unbiased estimation of
the effect of total trade `Ti` on output `Yi`
using two-stage least squares (2SLS).
The 2SLS estimator is
```
β_2sls = inv(Ti'Pz*Ti) Ti'Pz*Y
```
where using `Z = Tihat` we define `Pz = Z*inv(Z'Z)Z` as the projection matrix.

The F statistics used in evaluating the first stage are artificially inflated
if we use conventional calculations, since it assumes that the number of instruments,
`K` is equal to 1, the dimension of `Tihat`,
rather than the number of "distance instruments" used in the
gravity equation used to estimate the zero-th stage.
Thus, I correct the F statistics
by dividing by the true number of instruments used.
