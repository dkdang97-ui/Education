


## Project Overview

This project explores the relationship between school-level academic performance (ACT/SAT scores) and various socioeconomic characteristics of the surrounding school districts (such as median household income, unemployment rates, adult educational attainment, and family structures). Additionally, it integrates data from the National Center for Education Statistics (NCES) to incorporate detailed institutional metadata.
---

- **Objective:** Clean, merge, and analyze socioeconomic data from Census tracts and NCES school records to evaluate factors impacting school academic performance, including whether charter and non-charter high schools differ in average ACT scores.
- **Domain:** Education / Socioeconomic Analytics
- **Key Techniques:** Data cleaning and wrangling with pandas, merging datasets on a shared school ID, range checks for invalid values, filtering to high schools, iterative imputation of missing values (scikit-learn `IterativeImputer`), correlation and pair-plot EDA, and Welch's two-sample t-test
---

## Project Structure

```
education/
├── data/                                  # Raw and processed datasets
│   ├── EdGap_data.xlsx                    # Primary dataset: ACT/SAT scores & census-tract socioeconomic data
│   ├── ccd.csv                            # Secondary dataset: NCES Common Core of Data school directory
│   └── education_clean.csv                # Cleaned, merged dataset (output of Education_clean_data.ipynb)
├── code/                                  # Jupyter notebooks
│   ├── Education_clean_data.ipynb         # Loading, merging, quality control, imputation, export
│   └── Education_exploratory_data.ipynb   # EDA and charter vs. non-charter ACT comparison
├── reports/                               # Generated summary reports and exported figures
├── requirements.txt                       # Python dependencies
└── README.md                              # Project documentation

---

## Data

- **Source:**
      EdGap Data
      - Coverage: 2016 academic data.
      Source: National Center for Education Statistics (NCES) / Common Core of Data (CCD).
      - Coverage: 2016–2017 Academic Year.
  
### Description

- **EdGap_data.xlsx**: 7,986 schools and 7 columns. Each row is one school, identified by its NCES school ID, with the school's average ACT score, its share of students on free or reduced lunch, and characteristics of the surrounding census tract. Some tract variables have small numbers of missing values (13–25 per column).
- **ccd.csv**: 102,181 schools and 65 columns covering every public school in the U.S. Only 7 columns are used: school year, school ID, state, ZIP code, school type, school level, and charter status. The file needs `encoding='latin-1'` to load.
- **education_clean.csv**: The cleaned, merged file has **7,227 high schools** from **20 states** and 13 columns, with no missing values.

### Data dictionary (education_clean.csv)

| Column | Original name | Source | Description |
|---|---|---|---|
| `id` | NCESSCH School ID / NCESSCH | Both (join key) | NCES school identification number |
| `rate_unemployment` | CT Unemployment Rate | EdGap | Census tract unemployment rate |
| `percent_college` | CT Pct Adults with College Degree | EdGap | Census tract percentage of adults with a college degree |
| `percent_married` | CT Pct Childre In Married Couple Family | EdGap | Census tract percentage of children in a married-couple family |
| `median_income` | CT Median Household Income | EdGap | Census tract median household income (dollars) |
| `average_act` | School ACT average (or equivalent if SAT score) | EdGap | School's average ACT score (1–36). SAT averages are converted to an equivalent ACT score |
| `percent_lunch` | School Pct Free and Reduced Lunch | EdGap | Percentage of the school's students eligible for free or reduced-price lunch |
| `year` | SCHOOL_YEAR | CCD | Academic year (all rows are 2016–2017) |
| `state` | LSTATE | CCD | State abbreviation |
| `zip_code` | LZIP | CCD | ZIP code |
| `school_type` | SCH_TYPE_TEXT | CCD | Type of school (e.g., Regular, Alternative) |
| `school_level` | LEVEL | CCD | School level (all rows are High) |
| `charter` | CHARTER_TEXT | CCD | Charter status: Yes, No, or Not applicable |

Note that the attendance area for a school may contain multiple census tracts.

- **License:** Both datasets are publicly available. NCES data is published by the U.S. Department of Education.

---

## Analysis

**Education_clean_data.ipynb** (data cleaning):
- Loads both raw files and keeps only the 7 needed CCD columns. Renames all columns to short snake_case names.
- Left-joins CCD onto EdGap by school ID, with EdGap as the primary dataset. 88 EdGap schools had no match in CCD.
- Quality control: checks the min and max of every numeric column and sets impossible values to missing (ACT scores below 1 for 3 schools, negative free/reduced-lunch shares for 20 schools). Checks for duplicate rows (none).
- Keeps only high schools (7,230 rows), which also removes the 88 unmatched schools, then drops the 3 schools with no valid ACT score (7,227 rows).
- Fills the remaining missing socioeconomic values (11–20 per column) with scikit-learn's `IterativeImputer`.
- Plots the number of schools per state on a U.S. map (plotly) and saves education_clean.csv.

**Education_exploratory_data.ipynb** (exploration and analysis):
- Correlation heatmap and pair plots of the socioeconomic variables and average ACT, colored by charter status
- Box plots of the proportion variables and median income
- Charter vs. non-charter comparison of ACT scores:
  - Groups schools as **Charter** (`Yes`) or **Non-charter** (`No` + `Not applicable`). `Not applicable` comes almost entirely from Kentucky, which had no charter school law in 2016–17.
  - Summary statistics and a box plot of ACT by group
  - Welch's two-sample t-test with a 95% confidence interval for the difference in means

**Note: requires Python 3 with pandas, numpy, matplotlib, seaborn, scipy, scikit-learn, statsmodels, plotly, and openpyxl (to read the Excel file).**

---

## Results

**Conclusion 1:**
Average ACT is most closely tied to school poverty. The share of students on free or reduced lunch has the strongest correlation with ACT (r ≈ −0.78). The census-tract variables show moderate correlations: college degree share (r ≈ 0.46), median income (r ≈ 0.46), married-couple families (r ≈ 0.44), and unemployment (r ≈ −0.43).

**Conclusion 2:**
Charter high schools score lower on average. The 170 charter schools average 19.0 on the ACT, compared with 20.3 for the 7,057 non-charter schools, a gap of about 1.3 points. Charter scores are also more spread out (standard deviation 3.1 vs. 2.5).

**Conclusion 3:**
The charter gap is statistically significant. Welch's t-test gives t = −5.39 and p ≈ 2 × 10⁻⁷, and the 95% confidence interval for the difference is −1.8 to −0.8 ACT points. This is a simple comparison, though. It shows that the groups differ, not why, and charter schools in this data tend to serve lower-income areas.

---

## Authors

- Khoa Dang - https://github.com/dkdang97-ui

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgements

- School performance and socioeconomic data from [EdGap.org](http://edgap.org/)
- School directory data from the National Center for Education Statistics, Common Core of Data
- Datasets provided through the DATA 5100 course repository
- Built with pandas, NumPy, Matplotlib, seaborn, SciPy, scikit-learn, statsmodels, and plotly
