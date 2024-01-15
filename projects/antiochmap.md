---
title: Development Projects in Antioch, CA
description: Map of development projects in Antioch, CA using Python
permalink: /projects/antiochmap
---
## Background

## Process
Since not all of the projects had addresses, I decided to go with Assessor Parcel Numbers that almost all of the projects had. I sourced the parcel information from the [Contra Costa County Assessor](https://www.contracosta.ca.gov/552/Maps-Property-Information), which provided the shapefile for the parcels in the county but not the coordinates of each one. This is where I needed to use the Geopandas library to find the coordinates for each parcel and then drop the shapefile info and select only the parcels located within Antioch to save space.
```python
import geopandas as gpd

df = gpd.read_file("Parcels_Public_December2023.zip")
df = df.to_crs("4326")
df["Longitude"] = df["geometry"].representative_point().x
df["Latitude"] = df["geometry"].representative_point().y
df = df.drop("geometry", axis=1)
df = df.loc[df["S_CTY_ABBR"] == "ANT"]

df.to_csv("ANT_APNs.csv", index=False)
```

```python
import re
import requests
import pandas as pd
from bs4 import BeautifulSoup
import folium


def apn_search(proj_location):
    apn_regex = re.compile(r'\d\d\d-\d\d\d-\d\d\d')
    search = apn_regex.search(proj_location)
    apn = search.group().replace("-", "")
    return int(apn[1:])


def tooltip(proj_name, status, description, applicant):
    return f"Project Name: {proj_name}<br>" \
           f"Status: {status}<br>" \
           f"Description: {description}<br>" \
           f"Applicant: {applicant}"


url = "https://www.antiochca.gov/community-development-department/planning-division/current-projects/"
response = requests.get(url)
soup = BeautifulSoup(response.text, "html.parser")

data = []
table = soup.find("table", attrs={"id": "table_1"})
table_body = table.find("tbody")

rows = table_body.find_all('tr')
for row in rows:
    cols = row.find_all('td')
    cols = [ele.getText() for ele in cols]
    data.append([ele for ele in cols])

proj_df = pd.DataFrame(data, columns=["Use Type", "Project Name", "Project Number", "Location",
                                      "Description", "Status", "Applicant", "Plans",
                                      "Project Description", "Other"])
proj_df = proj_df.drop(["Plans", "Project Description", "Other"], axis=1)
#proj_df = pd.read_csv("test.csv")
proj_df["APN"] = ""

apn_df = pd.read_csv("ANT_APNs.csv")

apn_list = []
for index, row in proj_df.iterrows():
    try:
        apn_list.append(apn_search(row["Location"]))
    except AttributeError:
        apn_list.append(None)

proj_df["APN"] = apn_list

proj_df = pd.merge(proj_df, apn_df[["APN", "Longitude", "Latitude"]], on="APN", how="left")
proj_df.dropna(subset=["Longitude", "Latitude"], inplace=True)

antioch_map = folium.Map(location=[37.9755272, -121.8215628],
                         zoom_start=13,
                         tiles="OpenStreetMap")

for index, row in proj_df.iterrows():
    folium.Marker(location=[row["Latitude"], row["Longitude"]],
                  tooltip=tooltip(row["Project Name"], row["Status"], row["Description"],
                                  row["Applicant"]),
                  ).add_to(antioch_map)

antioch_map.save("antioch_map.html")
```
## The Final Result
{{< rawhtml >}}
<iframe src="https://rapg.me/projects/files/antioch_map.html"
 width="854" height="480"></iframe>
{{< /rawhtml >}}
