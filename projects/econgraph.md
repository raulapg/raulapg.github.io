---
title: Economic Analysis of Okun's Law
description: Analyzing Okun's Law since 2000
permalink: /projects/econgraph
---
## Background
As part of my Intermediate Macroeconomics class, I was tasked with analyzing a concept covered in class using real world data. I decided to do an anaylsis on whether Okun's Law (a theory stating there is an inverse relation between real GDP growth and the unemployment rate) has held true in the United States since 2000 by creating a graph in Python to make it easier to understand the data.

## Process
I started off by locating the datasets at the St. Louis Federal Reserve Bank's FRED. From there, I used an API to 
```python
from fredapi import Fred
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

fred = Fred(api_key='')

# Get data for real GDP
gdp_data = fred.get_series('GDPC1')
gdp_df = gdp_data.reset_index()
gdp_df.columns = ["Date", "GDP"]

# Get data for unemployment rate
unemployment_data = fred.get_series('UNRATE')
unemployment_df = unemployment_data.reset_index()
unemployment_df.columns = ["Date", "Unemployment Rate"]

# Merge unemployment rate and GDP data into one dataframe
okun_law_df = pd.merge(gdp_df, unemployment_df, on=['Date'])
okun_law_df = okun_law_df.loc[(okun_law_df['Date'].dt.year >= 2000) & (okun_law_df['Date'].dt.month >= 1)]

# Get percentage change for GDP and difference for unemployment rate
okun_law_df['Percentage Change in GDP'] = (okun_law_df['GDP'].pct_change())*100
okun_law_df['Change in Unemployment Rate'] = okun_law_df['Unemployment Rate'].diff()

# Create and show plot
sns.regplot(x='Percentage Change in GDP', y='Change in Unemployment Rate',
            data=okun_law_df, ci=None,
            line_kws={'color':'red'}).set(title="Okun's Law")
plt.show()
```
The final result:

![Okun's Law Quarterly 2000-2021.png](/projects/Okun's Law Quarterly 2000-2021.png)
## Conclusion
