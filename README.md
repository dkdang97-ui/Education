# ACT Scores, Marriage Percentage, and Charter Schools
 
This project explores whether **charter vs. non-charter status** and **marriage percentage** (the share of children in married-couple families) have an impact on a high school's average ACT score. It uses EdGap and NCES Common Core of Data (CCD) for **7,227 U.S. public high schools in 20 states** (2016–17).
 
> [!NOTE]
> All results are associations from observational data. They show what the ACT gap is related to, not what causes it.
 
---
 
## Research Questions
 
1. Which socioeconomic factors are most related to average ACT score?
2. Is there an ACT gap between charter and non-charter high schools?
3. Does marriage percentage explain part of that gap, and is its relationship with ACT the same for charter and non-charter schools?
 
---
 
## Project Structure
 
```text
education/
├── data/
│   ├── EdGap_data.xlsx                           # ACT/SAT scores and census-tract socioeconomic data
│   ├── ccd.csv                                   # NCES Common Core of Data school directory
│   └── education_clean.csv                       # Cleaned, merged dataset
├── code/
│   ├── Education_clean_data.ipynb                # Load, merge, quality checks, imputation, export
│   ├── Education_exploratory_data.ipynb          # EDA and charter vs. non-charter ACT comparison
│   └── Education_married_charter_analysis.ipynb  # Marriage percentage, charter status, regression models
├── reports/                                      # Written report and exported figures
├── requirements.txt                              # Python dependencies
└── README.md
```
 
---
 
## Data
 
| File | Source | Year | Rows × Columns | Notes |
|---|---|---|---|---|
| `EdGap_data.xlsx` | [EdGap.org](http://edgap.org/) | 2016 | 7,986 × 7 | One row per school (NCES ID). Average ACT, free/reduced lunch share, and census-tract variables. 13–25 missing values in some tract columns. |
| `ccd.csv` | [NCES CCD](https://nces.ed.gov/ccd/) | 2016–17 | 102,181 × 65 | Every U.S. public school. Only 7 columns are used. Load with `encoding='latin-1'`. |
| `education_clean.csv` | Merged | 2016–17 | 7,227 × 13 | High schools only, 20 states, no missing values. |
 
Both datasets are public. The CCD is published by the U.S. Department of Education.
 
---
 
## Data Dictionary
 
`education_clean.csv`
 
| Column | Original name | Source | Description |
|---|---|---|---|
| `id` | NCESSCH School ID / NCESSCH | Both (join key) | NCES school ID |
| `rate_unemployment` | CT Unemployment Rate | EdGap | Census tract unemployment rate |
| `percent_college` | CT Pct Adults with College Degree | EdGap | Share of adults in the tract with a college degree |
| `percent_married` | CT Pct Childre In Married Couple Family<sup>1</sup> | EdGap | Share of children in the tract in a married-couple family (marriage percentage) |
| `median_income` | CT Median Household Income | EdGap | Census tract median household income (dollars) |
| `average_act` | School ACT average (or equivalent if SAT score) | EdGap | School average ACT score (1–36). SAT averages are converted to ACT. |
| `percent_lunch` | School Pct Free and Reduced Lunch | EdGap | Share of students eligible for free or reduced-price lunch |
| `year` | SCHOOL_YEAR | CCD | School year (2016–2017 for all rows) |
| `state` | LSTATE | CCD | State abbreviation |
| `zip_code` | LZIP | CCD | ZIP code |
| `school_type` | SCH_TYPE_TEXT | CCD | Type of school (e.g., Regular, Alternative) |
| `school_level` | LEVEL | CCD | School level (High for all rows) |
| `charter` | CHARTER_TEXT | CCD | Charter status: Yes, No, or Not applicable |
 
<sup>1</sup> The typo is in the original EdGap column name.
 
> [!IMPORTANT]
> The census-tract variables describe the neighborhood around the school, not the students who attend it. A school's attendance area can also cover more than one census tract.
 
---
 
## Analysis
 
### 1. `Education_clean_data.ipynb`: Data cleaning
 
- Loads both raw files, keeps the 7 CCD columns needed, and renames all columns to snake_case.
- Left-joins CCD onto EdGap by school ID (EdGap is the main table). 88 EdGap schools had no match in CCD.
- Sets impossible values to missing: ACT below 1 (3 schools) and negative lunch shares (20 schools). No duplicate rows.
- Keeps only high schools (7,230 rows), which also removes the 88 unmatched schools, then drops the 3 schools with no valid ACT score (7,227 rows).
- Fills the remaining missing socioeconomic values (11–20 per column) with scikit-learn's `IterativeImputer`.
- Maps the number of schools per state with plotly and saves `education_clean.csv`.
 
### 2. `Education_exploratory_data.ipynb`: Exploration
 
- Correlation heatmap and pair plots of the socioeconomic variables and ACT, colored by charter status.
- Box plots of the proportion variables and median income.
- Groups schools as **Charter** (`Yes`) or **Non-charter** (`No` + `Not applicable`). Almost all `Not applicable` schools are in Kentucky, which had no charter school law in 2016–17.
- Summary statistics, box plot of ACT by group, and Welch's t-test with a 95% confidence interval.
 
### 3. `Education_married_charter_analysis.ipynb`: Marriage percentage and charter status
 
- Welch's t-tests comparing ACT and marriage percentage between charter and non-charter schools.
- OLS regression of ACT on marriage percentage, plus a quadratic term.
- Charter status added as a separate predictor, then as an interaction with marriage percentage.
- Full model (all five socioeconomic variables + charter) and reduced model (unemployment, college, lunch + charter), compared with ANOVA, R², MAE, and RMSE.
- Correlation heatmap and VIFs to check overlap between predictors.
- Robustness checks: robust (HC3) standard errors, dropping tracts with 0% or 100% marriage percentage, and state fixed effects.
 
---
 
## Results
 
| # | Finding | Key numbers |
|---|---|---|
| 1 | Free/reduced lunch is the strongest single predictor of ACT. | r ≈ −0.78 (college 0.46, income 0.46, marriage percentage 0.44, unemployment −0.43) |
| 2 | Charter schools score lower on average. | 19.02 vs. 20.33, gap −1.31 (Welch p ≈ 2 × 10⁻⁷, 95% CI −1.79 to −0.83) |
| 3 | Charter schools are in neighborhoods with lower marriage percentage. | 49% vs. 64% (p ≈ 2 × 10⁻¹⁴) |
| 4 | Marriage percentage alone is a strong predictor. | +0.58 ACT points per 10 percentage points, R² = 0.19 |
| 5 | Most of the charter gap goes away with controls. | −1.31 → −0.44 (with marriage percentage) → −0.17, p = 0.16 (all five variables) |
| 6 | Marriage percentage is less related to ACT in charter schools. | Slope 3.27 vs. 5.82, interaction p ≈ 0.0007 |
 
**Summary:** Charter vs. non-charter status and marriage percentage are both related to ACT on their own, but neither adds much once the community around the school is taken into account. Free/reduced lunch explains most of the variation in the full model (R² = 0.63).
 
> [!WARNING]
> With robust standard errors, the charter gap after controlling for marriage percentage is only borderline (p ≈ 0.07). The interaction result holds in every robustness check.
 
---
 
## Getting Started
 
**1. Clone the repository**
 
```bash
git clone https://github.com/dkdang97-ui/education.git
cd education
```
 
**2. Install dependencies**
 
```bash
pip install -r requirements.txt
```
 
Requires Python 3 with pandas, numpy, matplotlib, seaborn, scipy, scikit-learn, statsmodels, plotly, and openpyxl (for the Excel file).
 
**3. Update file paths**
 
Change the file paths at the top of each notebook to your local `data/` folder.
 
**4. Run the notebooks in order**
 
1. `Education_clean_data.ipynb`
2. `Education_exploratory_data.ipynb`
3. `Education_married_charter_analysis.ipynb`
 
---
 
## Author
 
**Khoa Dang** · [GitHub](https://github.com/dkdang97-ui)
 
---
 
## License
 
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.
 
---
 
## Acknowledgements
 
- School performance and socioeconomic data from [EdGap.org](http://edgap.org/)
- School directory data from the [National Center for Education Statistics, Common Core of Data](https://nces.ed.gov/ccd/)
- Datasets provided through the DATA 5100 course repository
- Built with pandas, NumPy, Matplotlib, seaborn, SciPy, scikit-learn, statsmodels, and plotly
 
