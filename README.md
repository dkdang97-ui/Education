


## Project Overview

This project explores the relationship between school-level academic performance (ACT/SAT scores) and various socioeconomic characteristics of the surrounding school districts (such as median household income, unemployment rates, adult educational attainment, and family structures). Additionally, it integrates data from the National Center for Education Statistics (NCES) to incorporate detailed institutional metadata.
---

- **Objective:** Clean, merge, and analyze socioeconomic data from Census tracts and NCES school records to evaluate factors impacting school academic performance.
- **Domain:** Education / Socioeconomic Analytics
- **Key Techniques:** Data cleaning and wrangling with pandas, missing-data detection, date-based imputation, and exploratory time-series plotting

---

## Project Structure

```
education/
├── data/                            # Raw and processed datasets
│   ├── EdGap_data.xlsx              # Primary dataset: ACT/SAT scores & socioeconomic data
│   └── ccd_sch_029_1617_w_1a_11212017.csv  # Secondary dataset: NCES school directory info
├── code/                            # Jupyter notebooks and Python scripts
│   └── exploratory_analysis.ipynb   # Data loading, cleaning, and pair-plot EDA
├── reports/                         # Generated summary reports and export figures
├── requirements.txt                 # Python dependencies
└── README.md                        # Project documentation

---

## Data

- **Source:**
      EdGap Data
      - Coverage: 2016 academic data.
      Source: National Center for Education Statistics (NCES) / Common Core of Data (CCD).
      - Coverage: 2016–2017 Academic Year.
  
- **Description:**
    **seattle_rain.csv**: The Seattle file comes from a single station and has 1,658 rows. It's missing 168 days entirely, and 22 more have no precipitation value.
    **portland_Maine.csv**: The Portland file has 20,780 rows from 29 stations around the area. I only used the Portland Jetport station, which has all 1,826 days
    **clean_seattle_portland_weather.csv**: The cleaned file has 3,652 rows (1,826 days × 2 cities) and three columns: date, city (SEA / PWM), and precipitation.
  
- **License:** NOAA data is in the public domain.

--- TO UPDATE LATER

## Analysis

**Seattle_Weather_week 1.ipynb**: 
- Loads both raw files, converts the dates, keeps only the Portland Jetport station, and joins the two cities on date.
- Reshapes the result into a tidy format, finds the missing days, and fills each one with that city's average for the same calendar day across the other years.
- Saves clean_seattle_portland_weather.csv.

**Seattle_Weather_week 2.ipynb**:
- Summary statistics, plus line, bar, box, and histogram plots by city and month
- Rainy-day proportions and heavy-rain days (more than 1 inch)
- Welch's t-tests on mean precipitation for each month
- z-tests on the share of rainy days for each month
- Seasonal shares, monthly heatmaps, a 30-day rolling average, and wet/dry streaks

**Note: requires Python 3 with pandas, numpy, matplotlib, seaborn, scipy, and statsmodels.**

---

## Results
**Conclusion 1:**
Portland gets more rain in total. It averaged about 48 inches a year, compared with about 41 for Seattle. It also had about three times as many heavy-rain days (68 vs. 23).

**Conclusion 2:**
Seattle gets rain more often. It rained on about half of Seattle's days, compared with about a third in Portland. Seattle's rainy-day share was significantly higher in 8 of the 12 months.
The seasons are very different. Almost half of Seattle's rain falls in winter, and summers are nearly dry. Portland's rain is spread fairly evenly through the year.

**Conclusion 3:**
Mean daily rain was only significantly different in three months. Seattle was wetter in January, and Portland was wetter in July and August.

---

## Authors

- Khoa Dang - https://github.com/dkdang97-ui/weather

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

---

## Acknowledgements

- Weather data from NOAA's National Centers for Environmental Information
- Built with pandas, NumPy, Matplotlib, seaborn, SciPy, and statsmodels
- Notebook structure videos based on the course's Seattle weather template
