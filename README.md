# Housing-Price-Spatial-Regression-Model
King-county-spatial-regression
# Spatial Regression Analysis of Housing Prices — King County, WA

**Author:** Tolulope Oladeji | Geography & GIS, University of Cincinnati  
**Course:** Exploratory Data Analytics with GIS  
**Date:** April 2026

---

## Project overview

This project investigates the spatial determinants of residential housing prices in King County, Washington, using a dataset of 21,597 property transactions. The central question is whether standard Ordinary Least Squares (OLS) regression is sufficient for modelling spatially distributed housing data, or whether explicit spatial regression models are required. The analysis progresses from baseline OLS through formal spatial autocorrelation testing to Spatial Lag and Spatial Error models, demonstrating the complete spatial econometrics workflow that is standard practice in published hedonic price research.

---

## What I did

**Step 1 — Baseline OLS models.** Two OLS models were built: Model 1 using 15 structural housing attributes (bedrooms, bathrooms, square footage, grade, condition, waterfront status, year built, and neighbourhood proxies), and Model 2 adding latitude and longitude as additional predictors. The dependent variable is log-transformed price, which is standard practice in hedonic housing models and improves normality of the error distribution.

**Step 2 — Diagnosing spatial autocorrelation.** Moran's I was computed on the OLS residuals using a K-Nearest Neighbours spatial weights matrix (k = 8, row-standardised). This formally tests whether the OLS assumption of independent residuals holds in spatially distributed data.

**Step 3 — Spatial regression models.** A Spatial Lag Model (SLM) and a Spatial Error Model (SEM) were fitted using the Generalised Method of Moments (GM) estimator from the PySAL `spreg` library. These models correct for the spatial dependence detected in Step 2 and produce more reliable coefficient estimates than OLS.

**Step 4 — Model comparison and residual mapping.** All four models were compared on Pseudo R², Moran's I on residuals, and spatial parameter significance. Choropleth residual maps were produced to visually verify the degree of spatial autocorrelation reduction across models.

---

## Summary of results

| Model | Adj R² / Pseudo R² | Moran's I (residuals) | Spatial parameter |
|---|---|---|---|
| OLS — no coordinates | 0.6540 | 0.4660 (p ≈ 0) | — |
| OLS — with lat/long | 0.6998 | 0.4331 (p ≈ 0) | lat/long as linear predictors |
| Spatial Lag Model | **0.8471** | **0.2312** (p ≈ 0) | ρ = 0.5326 |
| Spatial Error Model | 0.5963 | 0.7756 (p ≈ 0) | λ = 0.7392 |

**Key finding 1 — Spatial autocorrelation in OLS residuals is severe.**  
Moran's I on Model 1 residuals was 0.4660 (p ≈ 0), confirming strong positive spatial autocorrelation. Nearby houses have systematically similar residuals — meaning OLS treats spatially correlated errors as independent, which biases standard errors and makes hypothesis tests unreliable. This is a textbook violation of the OLS i.i.d. assumption and demonstrates why spatial regression is necessary for housing price data.

**Key finding 2 — Adding lat/lon as linear predictors does not resolve the problem.**  
Model 2 improved Adjusted R² from 0.654 to 0.700 and both coordinates were highly significant (p < 0.001), confirming that location carries independent predictive value. However, Moran's I on Model 2 residuals remained 0.4331 (p ≈ 0) — barely reduced from Model 1. Treating geographic coordinates as simple linear predictors cannot capture the non-linear, locally varying spatial structure of housing prices.

**Key finding 3 — The Spatial Lag Model is the best-fitting model.**  
The SLM achieved the highest Pseudo R² (0.8471) and the lowest Moran's I among all four models (0.2312), representing a 50.4% reduction in residual spatial autocorrelation relative to the OLS baseline. The significant positive ρ = 0.5326 confirms strong spatial price diffusion — properties near high-value homes command a premium independent of their own structural characteristics, consistent with neighbourhood spillover theory in real estate economics.

**Key finding 4 — The Spatial Error Model underperforms here.**  
The SEM produced a lower Pseudo R² (0.5963) and a higher Moran's I (0.7756) than the OLS baseline, indicating model misspecification. The large λ = 0.7392 suggests that spatial dependence in this dataset is better characterised as a substantive spillover process (captured by the SLM) rather than a nuisance arising from omitted variables (the SEM assumption). This outcome illustrates the importance of using LM diagnostic tests to guide model selection rather than defaulting to either spatial model.

---

## Repository contents

| File | Description |
|---|---|
| `KingCounty_Spatial_Regression.ipynb` | Fully annotated notebook — 35 cells, 13 sections |
| `King_County_Housing_Data.csv` | Dataset (21,597 King County house sales) |
| `01_price_map.png` | Spatial distribution of log sale prices across King County |
| `02_ols_comparison.png` | OLS Model 1 vs Model 2 — R² and residual distribution |
| `03_moran_scatterplots.png` | Moran's I scatterplots for both OLS models |
| `04_ols_residual_maps.png` | Geographic clustering of OLS residuals |
| `05_model_comparison.png` | R², Moran's I, and autocorrelation reduction across all four models |
| `06_residual_maps_comparison.png` | OLS vs SLM vs SEM residual maps side by side |

---

## How to run

```bash
pip install pandas numpy statsmodels matplotlib libpysal esda spreg
jupyter notebook KingCounty_Spatial_Regression.ipynb
```

> **Note for Windows users:** If you encounter a `SyntaxError: unterminated string literal` on cells containing Greek characters (ρ, λ), replace them with plain text `rho` and `lam` respectively. This is a Python 3.11 / Windows encoding issue.

---

## Skills demonstrated

- Hedonic housing price modelling with `statsmodels` OLS
- Spatial autocorrelation testing — Moran's I (`esda.moran`)
- K-Nearest Neighbours spatial weights matrix — k = 8, row-standardised (`libpysal`)
- Spatial Lag and Spatial Error models — GM estimator (`spreg`)
- Spatial econometrics model selection — LM diagnostics, Moran's I comparison
- Log-transformed dependent variable — interpretation and justification
- Choropleth residual mapping with `matplotlib`

---

## References

- Anselin, L. (1988). *Spatial Econometrics: Methods and Models.* Kluwer Academic Publishers.
- LeSage, J. & Pace, R.K. (2009). *Introduction to Spatial Econometrics.* CRC Press.
- Tobler, W. (1970). A computer movie simulating urban growth in the Detroit region. *Economic Geography*, 46, 234–240.
- Rey, S. & Anselin, L. (2010). PySAL: A Python library of spatial analytical methods. *Review of Regional Studies*, 37(1), 5–27.

---

*Part of PhD coursework — Geography & GIS, University of Cincinnati*  
*Author: Tolulope Oladeji · [github.com/tadedoyinsola](https://github.com/tadedoyinsola) · [LinkedIn](https://www.linkedin.com/in/tolulope-oladeji-aa988a192/)*
