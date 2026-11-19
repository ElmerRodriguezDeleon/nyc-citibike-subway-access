# Citi Bike and Subway Access in New York City

How well does Citi Bike fill the gaps in NYC's subway network? This project measures the distance from every Citi Bike station to its nearest subway station and finds the boroughs where both are weakest.

**Team:** Pyae Pyae Thu, Elmer Rodriguez, Richard Burton

## Questions
1. How close is each Citi Bike station to the nearest subway station?
2. Which boroughs rely most on Citi Bike (stations more than 1 km from the subway)?
3. Which stations near the subway have low bike availability (under 20%)?
4. How many stations are beyond a 5-minute walk (0.4 km) of the subway?
5. Which boroughs are underserved by both subway access and bike availability?

## Data
| Source | What it provides |
|---|---|
| [CityBikes API](https://api.citybik.es/v2/) | Live Citi Bike stations: location, free bikes, empty docks |
| [Transitland API](https://www.transit.land/) | NYC subway station locations |
| [NYC Open Data: Borough Boundaries](https://data.cityofnewyork.us/) | Official borough shapes, used to label each station |

## Method
- Pulled live station data from both APIs, paging through all subway results
- Labeled each Citi Bike station with its borough with a spatial join (GeoPandas) and removed New Jersey stations
- Calculated the geodesic distance from every Citi Bike station to every subway station and kept the nearest one
- Calculated bike availability (free bikes ÷ total docks) and summarized the results by borough

## Key findings
*Snapshot: Nov 19, 2026, about 5:11 PM ET. 2,370 Citi Bike stations, 493 subway stations.*

- The average Citi Bike station is **0.44 km** from the nearest subway station (median 0.34 km).
- **41%** of stations (972) sit beyond a 5-minute walk of the subway.
- **Queens** depends on Citi Bike the most: **21.7%** of its stations are more than 1 km from the subway, compared with 1.2% in Manhattan.
- **Queens (4.3%)** and **the Bronx (2.8%)** have the most stations that are both far from the subway and low on bikes. The Bronx also had the lowest average bike availability (32%).
- **671** stations near the subway had fewer than 20% of their docks filled at the time of the snapshot.

## Corrections to the original version
A review of the first version found and fixed:
- A reversed filter (`> 20` instead of `< 20`) that counted well-stocked stations as low-availability
- Incomplete API pagination (400 of 493 subway stations loaded)
- Borough labels assigned by rough coordinate boxes, which mislabeled stations and included New Jersey
- Station merges on name instead of ID, which created duplicate rows
- An API key hardcoded in the notebook. The key is now entered at run time and never stored.

Together, these fixes **reversed the main conclusion**. The first version pointed to Manhattan as the most underserved borough. The corrected data points to Queens and the Bronx.

## Limitations
- Bike availability is a single snapshot taken during evening rush hour and changes throughout the day.
- Distance is straight-line, not actual walking distance.
- Only subway stations are counted. Buses, PATH, and ferries are not, so Governors Island, for example, appears farther from transit than it really is.
- Staten Island has no Citi Bike stations, so it isn't in the results.

## How to run
1. Get a free API key at [transit.land](https://www.transit.land/).
2. Open `Citi_Bike_Subway_Access.ipynb` in Google Colab.
3. Click **Runtime → Run all**, then paste your key when prompted. The distance step takes a few minutes.

**Tools:** Python, pandas, GeoPandas, geopy, matplotlib, requests
