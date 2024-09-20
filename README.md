# Objective

The goal is to build a Power BI dashboard that will provide key insights into the 2024 Italian Grand Prix in Formula 1. This dashboard will offer information on:
- Qualifying performances
- Race pace
- Strategy 

It will serve as a comprehensive tool for analyzing the event, highlighting driver performance, tire choices, and key moments throughout the race.


# Data Source

What do we need to create this dashboard?

- Lap times
- Sector times
- Tire choices
- Positions
- Top speed information
- Pit stop data

The data will be gathered using the [FastF1](https://docs.fastf1.dev/) Python library, which is designed to provide easy access to Formula 1 data, including live timing, telemetry, and lap-by-lap details.


# Design

## Dashboard components required

What are the questions we want the dashboard to answer:
- What were the qualifying and race results?
- What was the margin in qualifying?
- What was the performance of each driver in each sector?
- What were the top speeds in qualifying and the race?
- What compounds did the drivers use in the race?
- How many pit stops did each driver make?
- How did tire life affect pace?
- What was the overall race pace for drivers and teams?

This may change as we proceed with the analysis.

## Tools

| Tool               | Purpose                               |
|-------------------|---------------------------------------|
| Jupyter Notebook   | Use for Python code                   |
| Pandas             | Handle and clean the data             |
| FastF1            | Import the data                       |
| Matplotlib         | Assist with Python visualizations      |
| Seaborn            | Create Python visualizations          |
| Power BI          | Visualization and dashboard creation   |
| PowerPoint         | Design templates for the dashboard    |


# Development

## Methodology

1. Get the data
2. Explore the data
3. Evaluate the data
4. Clean the data
5. Visualize the data in Python
6. Visualize the data in Power BI
7. Add Python visualizations into Power BI
8. Build design templates for the dashboard
9. Write documentation

## Data exploration

What have we learned?
- We have data that we don't need and can remove it.
- Lap times and sector times are in a problematic format, which we will need to change.
- We have all the information we need for the dashboard.
- The data is mostly clear.

All code can be found [here](https://github.com/MichalPac/2024-Italian-GP-Dashboard/blob/main/Italian-GP-Analysis-Notebook.ipynb)

## Data cleaning

What have we done?
- Dropped unnecessary columns.
- Converted lap times and sector times into seconds.

All code can be found [here](https://github.com/MichalPac/2024-Italian-GP-Dashboard/blob/main/Italian-GP-Analysis-Notebook.ipynb)

## Python Visualizations

- Box plot to show pace for each team in the race.
- Scatter plot to show the correlation between tire life and lap times.
- Line plot to show position changes during the race.

All code can be found [here](https://github.com/MichalPac/2024-Italian-GP-Dashboard/blob/main/Italian-GP-Analysis-Notebook.ipynb)

# Visualization

## Dashboard

### Qualifying tab

![quali](assets/images/quali_dash.gif)

### Race tab

![race](assets/images/race_dash.gif)

### Python tab

![python](assets/images/python_dash.gif)


## DAX

### 1. Qualifying Delta
The following DAX expression calculates the qualifying delta for each lap time

```DAX
QualiDelta = IF(
    quali[LapTimeSec] - MINX(ALL(quali), quali[LapTimeSec]) >= 0, 
    quali[LapTimeSec] - MINX(ALL(quali), quali[LapTimeSec]),
    BLANK()
) 
```

### 2. Sector 1 Best Time
The following DAX expression calculates best sector 1 time for each driver

```DAX
S1Best = IF(quali[Sector1TimeSec]=MINX(quali, quali[Sector1TimeSec]), 1, BLANK()) 
```
The same DAX code was used for the other sectors accordingly.

### 3. Best Top Speed Category
The following DAX expression calculates and outputs 1 if top speed is best overall

```DAX
BestTopSpeedY/N = IF(quali[SpeedST]=MAX(quali[SpeedST]), 1, BLANK())
```

### 4. Best Lap Time Category
The following DAX expression calculates and outputs 1 if lap time is best overall

```DAX
BestLapTimeY/N = IF(quali[LapTimeSec] = MIN(quali[LapTimeSec]), 1, BLANK())
```

### 5. Theoretical Best
The following DAX expression calculates theoretical best for all drivers
```DAX
TheoBest = 
CALCULATE(
    MIN(quali[Sector1TimeSec])+ MIN(quali[Sector2TimeSec])+MIN(quali[Sector3TimeSec]),
    ALLEXCEPT(quali, quali[Driver])
)
```

### 6. Theoretical Best Category
The following DAX expression calculates and outputs 1 if  theoretical time is best than best lap time, 2 if theoretical time is equal to best lap time
```DAX
TehoBestTheBestY/N = 
IF(
    quali[TheoBest]=MIN(quali[TheoBest]),1, 
    IF(OR(quali[TheoBest]=quali[LapTimeSec], quali[Driver]="LEC"), 2, BLANK())
)
```

### 7. Best Lap Time Per Driver
The following DAX expression calculates best lap time for each driver
```DAX
BestLapTimePerDriver = 
CALCULATE(
    MIN(quali[LapTimeSec]),
    ALLEXCEPT(quali, quali[Driver])
) 
```

### 8. Best Sector 1 Time During Fatest Lap
The following DAX expression outputs best sector 1 time for best lap time for each driver
```DAX
BestSector1TimeDuringFastestLap = LOOKUPVALUE(quali[Sector1TimeSec], quali[LapTimeSec], [BestLapTimePerDriver])
```
The same DAX code was used for the other sectors accordingly.

### 9.Sector 1 Delta
The following DAX expression calculates Sector 1 Delta for best lap time
```DAX
S1Delta = quali[BestSector1TimeDuringFastestLap] - MIN(quali[BestSector1TimeDuringFastestLap])
```
The same DAX code was used for the other sectors accordingly.

### 10. Best lap Time Overall
The following DAX expression calculates best lap time overall
```DAX
BestLapTimeOverall = 
CALCULATE(
    MIN(quali[LapTimeSec]),
    ALL(quali)
)
```

### 11. Best Sector 1 Time Per Driver
The following DAX expression calculates best sector 1 time for each driver 
```DAX
BestSector1TimePerDriver = 
CALCULATE(
    MIN(quali[Sector1TimeSec]),
    ALLEXCEPT(quali, quali[Driver])
)
```
The same DAX code was used for the other sectors accordingly.

### 12. Best Sector 1 Time Overall
The following DAX expression calculates best sector 1 time overall
```DAX
BestSector1TimeOverall = 
CALCULATE(
    MIN(quali[Sector1TimeSec]),
    ALL(quali)
)
```
The same DAX code was used for the other sectors accordingly.

### 13. Sector 1 Class
The following DAX expression outputs:
- 1 if the driver's best sector 1 time is equal to the overall best sector 1 time,
- 2 if the lap time is the driver's best and the driver's sector 1 time equals their best sector 1 time,
- 3 if the lap time is the driver's best and the driver's sector 1 time doesn't equals their best sector 1 time.
```DAX
Sector1Class = 
SWITCH(
    TRUE(),
    quali[Sector1TimeSec] = [BestSector1TimeOverall], 1,
    quali[LapTimeSec] = [BestLapTimePerDriver] && quali[BestSector1TimePerDriver] = [BestSector1TimeDuringFastestLap], 2,
    quali[LapTimeSec] = [BestLapTimePerDriver] && quali[Sector1TimeSec] <> [BestSector1TimePerDriver], 3
)
```
The same DAX code was used for the other sectors accordingly.

