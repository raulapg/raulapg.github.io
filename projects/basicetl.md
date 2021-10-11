---
title: Basic ETL Experiment
description: Experimenting with extraction, transformation, and loading with SQL, Python, and Tableau
permalink: /projects/basicetl
---
## Background
For this project, I wanted to create a simple data pipeline just to experiment with and try and develop some (very) basic data engineering skills. So I selected the Case-Shiller Index dataset from the Federal Reserve Bank of St. Louis' FRED using Python, sent it to a SQL database, and then created a basic graph in Tableau. For those that don't know, the Case-Shiller Index is an index that tracks the value of single-family detached homes in the United States using data from several other indexes. You can read more about it [here](https://www.investopedia.com/articles/mortgages-real-estate/10/understanding-case-shiller-index.asp)
## Process
I began by 
```python
from fredapi import Fred
from sqlalchemy import create_engine
import psycopg2
import pandas as pd

fred = Fred(api_key='')
data = fred.get_series('CSUSHPINSA')
cs_df = data.reset_index()
cs_df.columns = ['Date', 'Case-Shiller Index']
```
```python
conn = psycopg2.connect("dbname= user= password=")
c = conn.cursor()
c.execute("""CREATE TABLE case_shiller (
    date date,
    cs_index numeric(7, 3))""")
conn.commit()
```
```python
engine = create_engine('postgresql+psycopg2://postgres:genericpw@localhost:1111/db')
cs_df.to_sql('case_shiller', engine, if_exists='replace', index=False)

c.execute('SELECT * FROM case_shiller')
for row in c.fetchall():
    print(row)
```
The final result:

{{< rawhtml >}}
<iframe src="https://public.tableau.com/views/Case-ShillerIndex_16339121605040/Cash-ShillerIndexSince1987?:showVizHome=no&:embed=true"
 width="854" height="480"></iframe>
{{< /rawhtml >}}
