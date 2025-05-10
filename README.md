
# 📊 Spotify Listening Analysis Dashboard (Power BI)
![Spotify logo](https://github.com/VDhakad-Datamind/Spotify-PowerBi-Project/blob/main/Spotify_Logo_Final.png)

## 📘 Introduction

This Power BI project provides an in-depth analysis of a user’s Spotify listening habits over time. The dashboard visualizes various aspects of audio consumption such as album, artist, and track preferences, listening patterns across days and hours, and average track engagement. Through clean visual design and well-defined DAX measures, the report gives a 360-degree view of how the user interacts with music across years, platforms, and listening contexts.

-**Dataset Link**: [Spotify Dataset](https://github.com/VDhakad-Datamind/Spotify-PowerBi-Project/blob/main/spotify_history.csv)

## 🎯 Business Requirement

The objective of this dashboard is to:
- Analyze Spotify streaming history to uncover listening behaviors.
- Identify most played albums, tracks, and artists.
- Examine listening patterns by time of day and day of the week.
- Compare current year performance with previous years.
- Classify tracks based on listening frequency and engagement using a custom quadrant (CF Quadrant).
- Understand platform-specific usage.

## 🛠️ Tools and Technologies Used

- **Power BI Desktop** – for data modeling, visualization, and report development.
- **DAX (Data Analysis Expressions)** – for creating dynamic measures and KPIs.
- **Microsoft Excel/CSV** – as the primary data source (Spotify listening data export).
- **Power Query** – for data transformation and cleaning.
- **Date Table** – used to enable time intelligence functions like YOY calculations.


## Key Features & Analysis Performed

### 🔥 Listening Patterns Heatmap

- Visualized listening hours across all days of the week
- Helps identify user peak listening hours
- Total listening count shown per hour across week

### 🔄 Average Listening Time vs Track Frequency (Quadrant Chart)

- Scatter plot that categorizes songs into 4 quadrants:
- Low Time & High Frequency
- High Time & High Frequency
- High Time & Low Frequency
- Low Time & Low Frequency
- Sliders used to set dynamic thresholds for classification

### 📈 Albums, Artists, and Tracks Trend Over Years

- Line chart showing yearly growth
- KPIs comparing current year (CY) vs previous year (PY)
- YoY growth percentage displayed
- Highlights maximum and minimum years via markers

### 🏆 Top 5 Rankings

- Most played albums, artists, and tracks based on count
- Insights into top user preferences

### 📅 Weekday vs Weekend Listening Comparison

- Donut charts showing distribution of listening based on day type

### 📂 Drill Through Enabled

- Clicking on album, artist, or track details navigates to a detailed track-level report
- Enhances user interactivity and deeper insight on demand

### 🎛 Navigation Buttons & Parameters

- Used to switch between Overview, Listening Patterns and Drill through Details reports
- Drop-down filters for year, shuffle, skip status, and platform
- Slider parameters to control listening time and track frequency

## 🧠 DAX Measures (Organized by Functional Area)

### 🎧 Listening Engagement Metrics

```dax
Avg Listening time = AVERAGE(Sheet1[ms_played])/60000

Track Frequency = COUNTROWS(Sheet1)
```

### 📈 Custom Classification – CF Quadrant

```dax
CF Quadrant = 
VAR avgtime = [Avg Listening time] <= 'Listening time (min)'[Listening time (min) Value]
VAR Trackfreq = [Track Frequency] >= 'Track frequency parameter'[Track frequency parameter Value]

VAR Result =
    SWITCH(TRUE(), 
        avgtime && Trackfreq , 1,
        NOT avgtime && Trackfreq, 2,
        NOT avgtime && NOT Trackfreq , 3,
        avgtime && NOT Trackfreq , 4
    )
RETURN Result
```

### 📅 Year-Based Metrics (Current vs Previous)

```dax
Max year = MAX('Date'[Year])

Previous year = 
VAR _previous_year = MAX('Date'[Year])-1
RETURN CALCULATE(DISTINCTCOUNT(Sheet1[album_name]), 'Date'[Year] = _previous_year)

Previous year artist = 
VAR _previous_year = MAX('Date'[Year])-1
RETURN CALCULATE(DISTINCTCOUNT(Sheet1[artist_name]), 'Date'[Year] = _previous_year)

Previous yeartracks = 
VAR _previous_year = MAX('Date'[Year])-1
RETURN CALCULATE(DISTINCTCOUNT(Sheet1[track_name]), 'Date'[Year] = _previous_year)
```

### 📅 Current Year Metrics

```dax
CurrentYearAlbums = 
VAR _current_year = CALCULATE(MAX('Date'[Year]), 'Date', TREATAS(VALUES(Sheet1[Year Track date]), 'Date'[Year]), VALUES(Sheet1[platform]))
RETURN CALCULATE(DISTINCTCOUNT(Sheet1[album_name]), 'Date'[Year] = _current_year)

CurrentYearArtist = 
VAR _current_year = CALCULATE(MAX('Date'[Year]), 'Date', TREATAS(VALUES(Sheet1[Year Track date]), 'Date'[Year]), VALUES(Sheet1[platform]))
RETURN CALCULATE(DISTINCTCOUNT(Sheet1[artist_name]), 'Date'[Year] = _current_year)

CurrentYearTracks = 
VAR _current_year = CALCULATE(MAX('Date'[Year]), 'Date', TREATAS(VALUES(Sheet1[Year Track date]), 'Date'[Year]), VALUES(Sheet1[platform]))
RETURN CALCULATE(DISTINCTCOUNT(Sheet1[track_name]), 'Date'[Year] = _current_year)
```

### 📊 Year-over-Year KPIs

```dax
PY and YOY albums KPI = 
VAR _latest = [CurrentYearAlbums]
VAR _previous = [Previous year]
VAR YOY_growth = IF(NOT(ISBLANK(_previous)), DIVIDE(_latest - _previous, _previous, 0), BLANK())
RETURN IF(NOT(ISBLANK(_previous)), "vs PY: " & FORMAT(_previous, "#,##0") & " (" & FORMAT(YOY_growth, "0.00%") & ")", "No data")

PY and YOY artist KPI = 
VAR _latest = [CurrentYearArtist]
VAR _previous = [Previous year artist]
VAR YOY_growth = IF(NOT(ISBLANK(_previous)), DIVIDE(_latest - _previous, _previous, 0), BLANK())
RETURN IF(NOT(ISBLANK(_previous)), "vs PY: " & FORMAT(_previous, "#,##0") & " (" & FORMAT(YOY_growth, "0.00%") & ")", "No data")

PY and YOY tracks KPI = 
VAR _latest = [CurrentYearTracks]
VAR _previous = [Previous yeartracks]
VAR YOY_growth = IF(NOT(ISBLANK(_previous)), DIVIDE(_latest - _previous, _previous, 0), BLANK())
RETURN IF(NOT(ISBLANK(_previous)), "vs PY: " & FORMAT(_previous, "#,##0") & " (" & FORMAT(YOY_growth, "0.00%") & ")", "No data")
```

## Overview Report
![Overview](https://github.com/VDhakad-Datamind/Spotify-PowerBi-Project/blob/main/Overview%20-%20Spotify.png)

## Listening Patterns Report
![Listening_Patterns](https://github.com/VDhakad-Datamind/Spotify-PowerBi-Project/blob/main/Listening%20Patterns.png)


## Details - Drill through Report
![Drill_Through](https://github.com/VDhakad-Datamind/Spotify-PowerBi-Project/blob/main/Details%20-%20Drillthrough.png)






