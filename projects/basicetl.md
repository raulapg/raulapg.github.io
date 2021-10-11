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
engine = create_engine('postgresql+psycopg2://postgres:genericpw@localhost:1111/db')
cs_df.to_sql('case_shiller', engine, if_exists='replace', index=False)

c.execute('SELECT * FROM case_shiller')

for row in c.fetchall():
    print(row)
```
The final result:

{{< rawhtml >}}
<iframe src="https://public.tableau.com/views/Case-ShillerIndex_16339121605040/Cash-ShillerIndexSince1987?:showVizHome=no&:embed=true"
 width="645" height="955"></iframe>
{{< /rawhtml >}}