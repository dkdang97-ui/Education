# Compare the rain volume between Seattle and Portland, Maine


## Project Overview

Seattle has a reputation as one of the rainiest cities in the U.S. I wanted to see if that holds up against Portland, ME (the furthest city on the East Coast). I compared five years of daily precipitation data from 2018 to 2022 for both cities
---

- **Objective:** Produce a clean, tidy daily precipitation dataset for Seattle and Portland, Maine (2018–2022), then use it to compare how much and how often it rains in each city.
- **Domain:** Weather / Climate
- **Key Techniques:** Data cleaning and wrangling with pandas, missing-data detection, date-based imputation, and exploratory time-series plotting

---

## Project Structure

```
├── data/
│   ├── seattle_rain.csv                     # raw Seattle data
│   ├── portland_Maine.csv                   # raw Portland data
│   └── clean_seattle_portland_weather.csv   # cleaned data
├── code/
│   ├── Seattle_Weather_week 1.ipynb         # cleaning
│   └── Seattle_Weather_week 2.ipynb         # analysis
├── reports/
├── requirements.txt
└── README.md
```

---

## Data

- **Source:**
      https://www.ncei.noaa.gov/cdo-web/datasets/GHCND/locations/CITY:US230004/detail
      https://www.ncei.noaa.gov/cdo-web/datasets/GHCND/locations/CITY:US530018/detail
  
- **Description:**
    **seattle_rain.csv**: The Seattle file comes from a single station and has 1,658 rows. It's missing 168 days entirely, and 22 more have no precipitation value.
    **portland_Maine.csv**: The Portland file has 20,780 rows from 29 stations around the area. I only used the Portland Jetport station, which has all 1,826 days
    **clean_seattle_portland_weather.csv**: The cleaned file has 3,652 rows (1,826 days × 2 cities) and three columns: date, city (SEA / PWM), and precipitation.
  
- **License:** NOAA data is in the public domain.

---

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
