---
title: Economic Analysis
description: Analyzing Okun's Law post 2000
permalink: /projects/econgraph
---
## Background
As part of my Intermediate Macroeconomics class, I was tasked with analyzing a concept covered in class using real world data. I decided to do an anaylsis on whether Okun's Law (a theory stating there is an inverse relation between real GDP growth and the unemployment rate) has held true in the United States since 2000.

## Process

```python
from fredapi import Fred
import pandas as pd
import seaborn as sns
import matplotlib.pyplot as plt

fred = Fred(api_key='')

gdp_data = fred.get_series('GDPC1')
gdp_df = gdp_data.reset_index()
gdp_df.columns = ["Date", "GDP"]

unemployment_data = fred.get_series('UNRATE')
unemployment_df = unemployment_data.reset_index()
unemployment_df.columns = ["Date", "Unemployment Rate"]

okun_law_df = pd.merge(gdp_df, unemployment_df, on=['Date'])
okun_law_df = okun_law_df.loc[(okun_law_df['Date'].dt.year >= 2000) & (okun_law_df['Date'].dt.month >= 1)]

okun_law_df['Percentage Change in GDP'] = (okun_law_df['GDP'].pct_change())*100
okun_law_df['Change in Unemployment Rate'] = okun_law_df['Unemployment Rate'].diff()

sns.regplot(x='Percentage Change in GDP', y='Change in Unemployment Rate',
            data=okun_law_df, ci=None,
            line_kws={'color':'red'}).set(title="Okun's Law")
plt.show()
```
