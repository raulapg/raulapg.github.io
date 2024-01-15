---
title: Development Projects in Antioch, CA
description: Map of development projects in Antioch, CA using Python
permalink: /projects/antiochmap
---
## Background
Recently I found out that cities listed planned development projects on their websites, so I decided to take a look at my [city's website](https://www.antiochca.gov/community-development-department/planning-division/current-projects/) and those of neighboring cities. While my city listed all of the projects in a nice table, I found it difficult to know where these projects would be located since many of them only listed an Assessor Parcel Number (APN). This was in contrast to a neighboring city that visualized all of their projects on a map using ArcGIS, so I decided: why not make something like that for my city?
## Process
Since not all of the projects had addresses, I decided to go with Assessor Parcel Numbers that almost all of the projects had. I sourced the parcel information from the [Contra Costa County Assessor](https://www.contracosta.ca.gov/552/Maps-Property-Information), which provided the shapefile for the parcels in the county but not the coordinates of each one. This is where I needed to use the Geopandas library to find the coordinates for each parcel and then drop the shapefile column and select only the parcels located within Antioch to save space.
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
To start off, I imported the necessary libraries and created my functions: the first using regular expressions to find the APN and convert to the proper format and the second to create the map tooltip for each project.
```python
from bs4 import BeautifulSoup
import folium
import pandas as pd
import re
import requests


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
```
Next I used the requests library to load the page and then had BeautifulSoup find the table information and convert it to an appropriate format for my dataframe.
```python
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
proj_df["APN"] = ""
```
From there, I loaded up the file of APN coordinates that I created in the first step and created a loop that used my apn_search function to convert the APN from the city website to match the county's and handle any missing values. I then merged my two dataframes to add the coordinates to my dataframe containing the city projects and dropped any projects missing coordinates, since they could not be displayed.
```python
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
```
Lastly, I created the map and added all of the remaining projects, along with each project's name, status, description, and applicant using my tooltip function.
```python
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
