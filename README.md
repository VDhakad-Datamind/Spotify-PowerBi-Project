
# 📊 Spotify Listening Analysis Dashboard (Power BI)

## 📘 Introduction

This Power BI project provides an in-depth analysis of a user’s Spotify listening habits over time. The dashboard visualizes various aspects of audio consumption such as album, artist, and track preferences, listening patterns across days and hours, and average track engagement. Through clean visual design and well-defined DAX measures, the report gives a 360-degree view of how the user interacts with music across years, platforms, and listening contexts.

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

