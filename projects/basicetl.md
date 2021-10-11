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
 <div class='tableauPlaceholder' id='viz1633913296925' style='position: relative'><noscript><a href='#'><img alt='Cash-Shiller Index Since 1987 ' src='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ca&#47;Case-ShillerIndex_16339121605040&#47;Cash-ShillerIndexSince1987&#47;1_rss.png' style='border: none' /></a></noscript><object class='tableauViz'  style='display:none;'><param name='host_url' value='https%3A%2F%2Fpublic.tableau.com%2F' /> <param name='embed_code_version' value='3' /> <param name='site_root' value='' /><param name='name' value='Case-ShillerIndex_16339121605040&#47;Cash-ShillerIndexSince1987' /><param name='tabs' value='no' /><param name='toolbar' value='yes' /><param name='static_image' value='https:&#47;&#47;public.tableau.com&#47;static&#47;images&#47;Ca&#47;Case-ShillerIndex_16339121605040&#47;Cash-ShillerIndexSince1987&#47;1.png' /> <param name='animate_transition' value='yes' /><param name='display_static_image' value='yes' /><param name='display_spinner' value='yes' /><param name='display_overlay' value='yes' /><param name='display_count' value='yes' /><param name='language' value='en-US' /><param name='filter' value='publish=yes' /></object></div>                <script type='text/javascript'>                    var divElement = document.getElementById('viz1633913296925');                    var vizElement = divElement.getElementsByTagName('object')[0];                    vizElement.style.width='100%';vizElement.style.height=(divElement.offsetWidth*0.75)+'px';                    var scriptElement = document.createElement('script');                    scriptElement.src = 'https://public.tableau.com/javascripts/api/viz_v1.js';                    vizElement.parentNode.insertBefore(scriptElement, vizElement);                </script>
{{< /rawhtml >}}
