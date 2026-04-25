# Sectoral Macroeconomic Damage Functions of Weather Extremes

**Authors:** Francesco Granella, Johannes Emmerling  
**Affiliation:** RFF-CMCC European Institute on Economics and the Environment; Euro-Mediterranean Center on Climate Change  
**Funding:** European Union's Horizon Europe research and innovation programme, grant agreement no. 101081369 (SPARCCLE)

---

## Overview

This repository provides sectoral macroeconomic damage functions linking weather extremes to economic growth, along with country-level projections of weather extremes under five SSP-RCP scenarios from 2015 to 2100. The damage functions cover four outcomes — total gross regional product (GRP), agriculture, manufacturing, and services — and are estimated separately for Europe and for the global sample.

---

## Repository Contents

| File | Description |
|------|-------------|
| `sectoral_damage_functions.pdf` | Full variable definitions, damage coefficients for Europe and globally |
| `weather_extremes_country_ssp_2015_2100.parquet` | Country-level projections of weather extremes, population-weighted using 2020 weights, 2015–2100 |

---

## Damage Functions

Coefficients were estimated using Post-LASSO (Adaptive LASSO for variable selection, followed by OLS). The model takes the form:

```
GDP per capita growth(i,t) = Baseline growth(i,t)
                           + β₁ · Δ(t,t₀) Extreme_1(i,t)
                           + β₂ · Δ(t,t₀) Extreme_2(i,t)
                           + ...
```

where `i` indexes countries/regions and `t` indexes years. `Δ(t,t₀)` denotes the change in an extreme relative to a baseline period `t₀`. All regressions include region fixed effects, year fixed effects, and regional linear time trends.

> **Note:** Standard errors for Post-LASSO estimates are not valid for inference.

### European Coefficients

| Variable | GRP | Agriculture | Manufacturing | Services |
|----------|-----|-------------|---------------|----------|
| PET | 0.000215 | | 0.000236 | 0.000192 |
| RX | 0.000179 | | | 0.000265 |
| TVAR | −0.0423 | | −0.0396 | −0.0559 |
| SPEI | −0.0000660 | −0.00290 | −0.000915 | −0.000427 |
| CW | 0.0000602 | 0.000183 | | −0.000000178 |
| TN | −0.00904 | | −0.0201 | −0.00918 |
| HW | −0.00137 | 0.000106 | | |
| RR | | −0.000111 | | |

### Global Coefficients

| Variable | GRP | Agriculture | Manufacturing | Services |
|----------|-----|-------------|---------------|----------|
| RR | 0.0000222 | 0.00000107 | 0.00000620 | |
| TVAR | −0.0267 | −0.00863 | −0.00718 | −0.0230 |
| WD | −0.000303 | | | −0.000237 |
| SPEI | −0.0000903 | −0.000281 | | −0.0000975 |
| CW | −0.0000735 | | −0.000134 | −0.000149 |
| TX | −0.00223 | | | −0.00282 |
| HW | | −0.000794 | | |
| RX | | 0.0000243 | | |
| TN | | 0.00637 | | |

Empty cells indicate that the variable was not selected by the LASSO for that outcome.

---

## Projection Data

**File:** `weather_extremes_country_ssp_2015_2100.parquet`

The file contains annual country-level projections of weather extremes, population-weighted using 2020 weights.

- **Rows:** 110,510
- **Countries:** 257 (ISO 3166-1 alpha-3 codes)
- **Years:** 2015–2100
- **Scenarios:** 5 SSP-RCP combinations (`ssp119`, `ssp126`, `ssp245`, `ssp370`, `ssp585`)

### Columns

| Column | Description | Unit |
|--------|-------------|------|
| `iso3` | Country code (ISO 3166-1 alpha-3) | — |
| `year` | Year | — |
| `ssp` | SSP-RCP scenario | — |
| `TX` | Mean daily maximum temperature | °C |
| `TN` | Mean daily minimum temperature | °C |
| `TM` | Mean daily temperature | °C |
| `TVAR` | Intra-annual temperature variability | °C |
| `RR` | Total annual precipitation | mm |
| `RX` | Maximum 5-day cumulative precipitation | mm |
| `WD` | Wet days | days year⁻¹ |
| `PEXT` | Extreme precipitation | mm year⁻¹ |
| `HW` | Heatwave severity | °C·days year⁻¹ |
| `CW` | Cold wave severity | °C·days year⁻¹ |
| `SPI` | Standardised Precipitation Index | unitless |
| `SPEI` | Standardised Precipitation–Evapotranspiration Index | unitless |

### Variable Definitions

| Indicator | Description |
|-----------|-------------|
| **TX** | Mean daily maximum temperature. Daily maxima averaged to monthly, then annual mean. |
| **TN** | Mean daily minimum temperature. Daily minima averaged to monthly, then annual mean. |
| **TM** | Mean daily temperature. Average of monthly TX and TN, then annual mean. |
| **TVAR** | Intra-annual temperature variability. Monthly standard deviation of daily mean temperature around the monthly mean, averaged across months. |
| **RR** | Total annual precipitation. Sum of daily rainfall over the calendar year. |
| **RX** | Maximum 5-day cumulative precipitation. Highest rolling 5-day rainfall total in the year. |
| **WD** | Wet days. Count of days with daily precipitation exceeding 1 mm. |
| **PEXT** | Extreme precipitation. Annual sum of daily precipitation on days exceeding the climatological 99.5th percentile threshold (derived from daily 99th percentile values over the 1981–2010 baseline). |
| **HW** | Heatwave severity. Cumulative sum of daily TX exceedances above the smoothed 90th-percentile threshold (1981–2010 baseline) during events of ≥5 consecutive days where TX also exceeds 30°C, summed over the 6-month summer season. |
| **CW** | Cold wave severity. Analogous to HW but computed using TN during the 6-month winter season, with an absolute threshold of 0°C. |
| **SPI** | Standardised Precipitation Index. Annual sum of monthly SPI-6 values during drought events (≥3 consecutive months with SPI ≤ −1, continuing while SPI ≤ 0); excludes hyper-arid (AI ≤ 0.05) and frozen areas (mean monthly PET < 30.4 mm month⁻¹); reference period 1950–2010, Gamma distribution. |
| **SPEI** | Standardised Precipitation–Evapotranspiration Index. Analogous to SPI but incorporating the precipitation–PET balance; log-logistic distribution. |

> **Note:** `PET` (Potential evapotranspiration, estimated via the Hargreaves formula) and `SNX` (Maximum 3-day cumulative snowfall) are defined in the damage function document but are not included in this projection file as standalone columns; PET is used internally to construct SPEI and the drought masks for SPI/SPEI.

---

## Usage Example

```python
import pandas as pd

# Load projection data
df = pd.read_parquet("weather_extremes_country_ssp_2015_2100.parquet")

# Example: compute the change in TVAR relative to a baseline (2015)
baseline = df[df["year"] == 2015].set_index(["iso3", "ssp"])["TVAR"]
df = df.join(baseline.rename("TVAR_baseline"), on=["iso3", "ssp"])
df["delta_TVAR"] = df["TVAR"] - df["TVAR_baseline"]

# Apply the European GRP damage function coefficient for TVAR
beta_TVAR_grp_europe = -0.0423
df["impact_TVAR"] = beta_TVAR_grp_europe * df["delta_TVAR"]
```

Repeat for each selected variable and sum contributions to obtain the total weather-driven deviation from baseline GRP growth.

---

## Citation

If you use this material, please cite the associated paper and acknowledge funding from the SPARCCLE project (Horizon Europe grant agreement no. 101081369).

---

## License

To be specified.
