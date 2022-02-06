---
title: EV Chargers Map
description: Map of EV Chargers in the U.S. created using Python and ArcGIS Online
permalink: /projects/evchargers
---
## Background
For this project, I wanted to expand my skills working on APIs from my last ETL project, while also creating something more useful than my last chart.
## Process
```python
import requests
import json
import pandas as pd

url = "https://developer.nrel.gov/api/alt-fuel-stations/v1.json?api_key=abc123&status=E&access=public&fuel_type=ELEC"

api_response = requests.get(url)
stations = json.loads(api_response.text)

df = pd.DataFrame(columns=["Station Name", "Directions", "Street Address", "City", "State", "ZIP", "Country",
                           "Latitude", "Longitude", "Network", "DCFC Chargers", "Level 2 Chargers", "Level 1 Chargers",
                           "Connector Types", "Tesla Connector", "CCS Connector", "CHAdeMO Connector",
                           "J1772 Connector"])
df["Connector Types"] = df["Connector Types"].astype(object)

for station in stations.get("fuel_stations"):
    row = [station["station_name"], station["intersection_directions"], station["street_address"], station["city"],
           station["state"], station["zip"], station["country"], station["latitude"], station["longitude"],
           station["ev_network"], station["ev_dc_fast_num"], station["ev_level2_evse_num"],
           station["ev_level1_evse_num"], station["ev_connector_types"], None, None, None, None]
    df.loc[len(df)] = row


df["Connector Types"] = df["Connector Types"].fillna("N/A")
df = df.drop(df[df["Connector Types"] == "N/A"].index)

for i in range(len(df["Connector Types"])):
    df.iloc[i, 14] = "TESLA" in df.iloc[i, 13]
    df.iloc[i, 15] = "J1772COMBO" in df.iloc[i, 13]
    df.iloc[i, 16] = "CHADEMO" in df.iloc[i, 13]
    df.iloc[i, 17] = "J1772" in df.iloc[i, 13]


df["DCFC Chargers"] = df["DCFC Chargers"].fillna(0)
df["Level 2 Chargers"] = df["Level 2 Chargers"].fillna(0)
df["Level 1 Chargers"] = df["Level 1 Chargers"].fillna(0)
df = df.fillna("N/A")
df = df.drop(["Connector Types"], axis=1)
df.to_csv("ev_chargers.csv", index=False)
```

{{< rawhtml >}}
<iframe src="https://experience.arcgis.com/experience/167d2d53da934f8eb76f47dd52b8ae6c/"
 width="854" height="480"></iframe>
{{< /rawhtml >}}
