---
title: Basic ETL Experiment
description: Experimenting with extraction, transformation, and loading with SQL, Python, and Tableau
permalink: /projects/basicetl
---
## Background

## Process
```python
from fredapi import Fred
from sqlalchemy import create_engine
import psycopg2
import pandas as pd

fred = Fred(api_key='')
data = fred.get_series('CSUSHPINSA')
cs_df = data.reset_index()
cs_df.columns = ['Date', 'Case-Shiller Index']

conn = psycopg2.connect("dbname= user= password=")
c = conn.cursor()
c.execute("""CREATE TABLE case_shiller (
    date date,
    cs_index numeric(7, 3))""")
conn.commit()
engine = create_engine('postgresql+psycopg2://postgres:Gibraltar@localhost:5432/experiments')
cs_df.to_sql('case_shiller', engine, if_exists='replace', index=False)

c.execute('SELECT * FROM case_shiller')

for row in c.fetchall():
    print(row)
```
