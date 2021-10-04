---
title: Flight Delay Analysis
description: Analyzing 2015 U.S. flight delays
permalink: /projects/flightdelay
---
## Background

## Process
```python
import pandas as pd
import re

flight_data = 'data\\flights.csv'
airport_data = 'data\\AirportID_to_IATAID.csv'
flight_df = pd.read_csv(flight_data, dtype={'ORIGIN_AIRPORT': 'string', 'DESTINATION_AIRPORT': 'string'})
airport_df = pd.read_csv(airport_data)

airportRegex = re.compile(r'\d\d\d\d\d')
for i in range(len(flight_df) + 1):

    origin_airport, destination_airport = flight_df.iloc[i, 7], flight_df.iloc[i, 8]
    origin_match = airportRegex.search(str(origin_airport))
    destination_match = airportRegex.search(str(destination_airport))

    if origin_match != None:
        airport_lookup = airport_df[(airport_df['AIRPORT_ID'] == int(origin_airport))]
        flight_df.iloc[i, 7] = airport_lookup.iloc[0, 1]

    if destination_match != None:
        airport_lookup = airport_df[(airport_df['AIRPORT_ID'] == int(destination_airport))]
        flight_df.iloc[i, 8] = airport_lookup.iloc[0, 1]

flight_df.to_csv('flights_fixed.csv', index=False)
```
