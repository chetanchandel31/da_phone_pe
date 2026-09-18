# PhonePe Data Analytics

Analysis of PhonePe transaction data (Q1 2018 – Q2 2021) across Indian states and districts, combined with population demographics, covering transaction trends, device usage patterns, and demographic correlations.

[**View full notebook with rendered output on Github**](https://github.com/chetanchandel31/da_phone_pe/blob/main/notebooks/01_phone_pe_analysis.ipynb)
 <!-- - [**View full notebook with rendered output on nb-viewer**](https://nbviewer.org/github/chetanchandel31/da_phone_pe/blob/main/notebooks/01_phone_pe_analysis.ipynb) -->
<!-- - [**View and run this notebook on google colab**](https://colab.research.google.com/github/chetanchandel31/da_phone_pe/blob/main/notebooks/01_phone_pe_analysis.ipynb) -->

## Dataset

The dataset provided is an Excel workbook containing five related datasets:

- State Transaction and Users
- District Transaction and Users
- State Transaction Type Split
- State Device Data
- District Demographics

The data covers Q1 2018 to Q2 2021.


## Tasks Covered

The analysis covers tasks including:

- Dataset structure and basic exploration (1.1)
- Summary statistics and data types (1.2)
- Missing value analysis (1.3)
- State and district counts (1.4)
- Missing value investigation and data-cleaning (1.5)
- Transaction trends by state (2.1)
- Most common transaction type per state and quarter (2.2)
- Top device brand by registered users, per state (2.3)
- Top district by population, per state (2.4)
- Average transaction value (ATV) by state (2.5)
- App usage trends over time (2.6)
- Transaction type distribution, most recent quarter (2.7)
- District name-to-code mapping (2.8)
- State vs. district-level data consistency checks (3.1)
- Registered users-to-population ratio by state (4.1)
- Population density vs. transaction volume correlation (4.2)
- Average transaction amount per user, by state (4.3)
- Device brand usage ratio by state (4.4)
- Transactions and amount over time, selected state (5.1)
- Transaction type distribution, selected state and quarter (5.2)
- District population density, selected state (5.3)
- Nationwide trends and patterns in transaction data (6.1)
- Demographic vs. transaction data correlation, summarized (6.2)
- Key findings and actionable recommendations (6.3)

## Data Cleaning

The raw dataset required several cleaning and validation steps before analysis (Task 1.5).

Key areas addressed included:

- **A single missing Amount value** - Andhra Pradesh's Q1 2021 `Amount (INR)` was null (causing `ATV (INR)` to compute as `0`). Estimated a reasonable `ATV` by averaging nearby quarters (2020 Q3, 2020 Q4, 2021 Q2), then recomputed `Amount = Transactions × ATV`.
- **"Not measured" vs. genuinely zero** - `App Opens` was `0` for every row across every state and district from Q1 2018 through Q1 2019, then never `0` again afterward - a clean boundary indicating the metric simply wasn't tracked yet, not that usage was zero. Replaced with `NaN` in both `State_Txn and Users` and `District_Txn and Users`.
- **Unresolvable missing district codes** - two districts (Mirpur, Muzaffarabad) had no `Code` value and used internal PhonePe-specific conventions (e.g. `JK01`, `JK02`) not derivable from anywhere else in the dataset. Since these codes couldn't be found in `District Demographics` either, they were left as missing rather than guessed.
- **Zeros that are real, not missing** - `Shi Yomi` and `Anjaw`, among the lowest-population districts in the dataset, had missing `Transactions`, `Amount (INR)`, and `ATV (INR)` values. Given their very low population, this was treated as genuinely low/no transaction activity rather than a data gap - `Transactions`/`Amount` weren't imputed, and `ATV` was left as `NaN` rather than `0` to avoid implying transactions existed with zero average value.
- **Disguised zeros in demographic data** - `Population`, `Area (sq km)`, and `Density` can't truly be `0`, so hidden `0` values (from Excel number formatting) were replaced with `NaN`. One row was missing only `Density`, which was recoverable and imputed directly via `Density = Population / Area`.

## Data Merging & Aggregation

Several tasks required combining data across sheets - district-level transaction data was merged with district demographics (matched first on `State`+`District`, then retried against the `Alternate Name` column for unmatched rows), and state-level transaction data was merged with device-brand data to compute usage ratios. District-level totals were also cross-checked against state-level totals to confirm consistency, surfacing one traceable discrepancy tied to the Andhra Pradesh imputation above.

## Visualizations

The project uses Python visualization libraries to explore trends and comparisons, including:

- Line charts for time-series trends (transactions, amount, registered users, app opens)
- Column and horizontal bar charts for state/district comparisons
- Stacked bar charts for transaction-type and device-brand distribution across states
- A pie chart for transaction-type breakdown in a selected state and quarter
- Scatter plots (linear-scaled and log-scaled) for correlating population density with transaction volume

Visualizations are accompanied by short observations highlighting notable patterns and anomalies in the data.

## **Key Findings:**

- **Growth**: Transactions, Amount, and Registered Users all grew consistently from 2018 to mid-2021, consistent with broader UPI adoption in India, with a temporary dip around Q2-2020 followed by a steep rise which aligns with pandemic-driven digital payments growth.

  App Opens follow a similar pattern from Q2-2019 onward, with a dip around Q2-2020 followed by recovery and strong growth. New Registered Users, however, fluctuated within a relatively stable range from 2019 onward rather than continuing the sharp growth seen in 2018. (Task 6.1).
- **Payment mix**: In Karnataka (the selected state for this analysis), Merchant payments (46.1%) and Peer-to-peer payments (40.8%) dominate transaction volume, with Recharge & bill payments and Financial Services/Others making up a small remainder (Task 5.2).

  The nationwide breakdown (Task 6.1) shows a similar overall pattern, with Peer-to-peer and Merchant payments as the two largest categories across all states.

  P2P transactions also consistently have a much higher ATV than Merchant transactions, despite their relatively similar transaction counts. This difference contributes to fluctuations in the overall ATV, particularly during the earlier years of the dataset (Task 6.1).
- **Device usage**: Xiaomi is the top device brand by registered users in nearly every state (35/36), with Samsung leading only in Sikkim (Task 2.3).
- **State-level disparities**: Top and bottom states by transaction volume, ATV, and users/population ratio differ significantly, indicating uneven regional adoption (Tasks 2.1, 2.5, 4.1).
- **Density vs. transaction volume**: Population density and transaction volume show a real but non-linear relationship, Pearson correlation is weak (0.25) due to outlier districts, but Spearman (0.45, moderate) and the log-log scatter plot (clear upward trend) both indicate density does relate to volume once outliers and scale are accounted for (Task 4.2).
- **Data gaps**: `App Opens` wasn't tracked before Q2-2019. A small number of low-population districts (e.g., Anjaw, Shi Yomi) show genuinely low/no transaction activity rather than missing data (Task 1.5).

## **Recommendations:**

- **Target underperforming states/districts**: States/districts with low transaction volume or low users-to-population ratio (identified in Tasks 2.1, 4.1) are candidates for focused marketing or merchant-onboarding pushes.
- **Invest in both P2P and Merchant infrastructure, with different priorities**: P2P dominates transaction amount nationwide (higher-value transfers), while Merchant payments is comparable or even leads by transaction-count in some states/quarters (e.g. Karnataka, Task 5.2) and shows strong growth (Task 6.1). 

  This suggests P2P reliability/scale matters for handling large transaction values, while Merchant-side tooling (QR codes, settlement speed) matters for supporting high-frequency, lower-value usage.
- **Investigate low-density-but-high-volume districts**: Districts that break the density-volume correlation (e.g., high volume despite moderate density) may reveal replicable success factors, worth a dedicated study.
- **Device-specific optimization**: Given Xiaomi's dominant share nearly everywhere, ensure app performance/UX is well-optimized specifically for that device ecosystem, since it likely represents the majority experience.
- **Prepare for continued growth**: Since `Registered Users` shows no plateau through 2021 and the pandemic accelerated adoption further, infrastructure/scaling decisions should assume continued strong growth, not a maturing/saturating market.

## Tech Stack

Python, Pandas, Matplotlib, Seaborn, Jupyter, [uv](https://github.com/astral-sh/uv), VS Code.

## Project structure
```bash
da_phone_pe/
├── assets/ # chart images embedded in this README
├── data/
│ ├── raw/
│ │ └── phonepe_dataset.xlsx
│ └── processed/
│ └── district_name_code_mapping.csv
├── notebooks/
│ └── 01_phonepe_analysis.ipynb
├── pyproject.toml
├── uv.lock
└── README.md
```

Data loads from the local file by default, if it's not found (e.g. running outside a full clone or on Google Colab), it falls back to fetching the raw data file directly from this repo's raw GitHub URL.

## Running it locally

```
git clone https://github.com/chetanchandel31/da_phone_pe.git
cd da_phone_pe
uv sync
```


The project uses `uv` for Python dependency and environment management.

The notebook can be opened and executed using Jupyter or the Jupyter extension in VS Code. Open `notebooks/01_phonepe_analysis.ipynb` and run all cells.