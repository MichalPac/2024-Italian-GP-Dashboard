# 2024 Italian Grand Prix Analysis
![Poster](assets/images/Monza_poster.jpg)




# Table of contents
- [Objective](#Objective)
- [Data Source](#Data-Source)
- [Design](#Design)
  - [Dashboard components required](#Dashboard-components-required)
  - [Tools](#Tools)
- [Development](#Development)
  - [Methodology](#Methodology)
  - [Data Exploration](#Data-Exploration)
  - [Data Cleaning](#Data-Cleaning)
  - [Python Visualizations](#Python-Visualizations)
- [Visualization](#Visualization)
  - [Dashboard](#Dashboard)
    - [Qualifying Tab](#Qualifying-Tab)
    - [Race Tab](#Race-Tab)
    - [Python Tab](#Python-Tab)
  - [DAX for Qualifying Dataset](#DAX-for-Qualifying-Dataset)
  - [DAX for Race Dataset](#DAX-for-Race-Dataset)
  - [Python Scripts in Power BI](#Python-Scripts-in-Power-BI)
- [Analysis](#Analysis)
- [Conclusion](#Conclusion)
- [Appendix](#Appendix)


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
| GitHub              | Host project documentation        |


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

## Data Exploration

What have we learned?
- We have data that we don't need and can remove it.
- Lap times and sector times are in a problematic format, which we will need to change.
- We have all the information we need for the dashboard.
- The data is mostly clear.

All code can be found [here](https://github.com/MichalPac/2024-Italian-GP-Dashboard/blob/main/Italian-GP-Analysis-Notebook.ipynb)

## Data Cleaning

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

### Qualifying Tab

![quali](assets/images/quali_dash.gif)

### Race Tab

![race](assets/images/race_dash.gif)

### Python Tab

![python](assets/images/python_dash.gif)


## DAX for Qualifying Dataset

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
The following DAX expression outputs 1 if driver's sector 1 time = best overal sector 1 time

```DAX
S1Best = IF(quali[Sector1TimeSec]=MINX(quali, quali[Sector1TimeSec]), 1, BLANK()) 
```
The same DAX code was used for the other sectors accordingly.

### 3. Best Top Speed Category
The following DAX expression outputs 1 if top speed is best overall

```DAX
BestTopSpeedY/N = IF(quali[SpeedST]=MAX(quali[SpeedST]), 1, BLANK())
```

### 4. Best Lap Time Category
The following DAX expression coutputs 1 if lap time is best overall

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
The following DAX expression outputs 1 if  theoretical time is best than best lap time, 2 if theoretical time is equal to best lap time
```DAX
TehoBestTheBestY/N = 
IF(
    quali[TheoBest]=MIN(quali[TheoBest]),1, 
    IF(quali[TheoBest]=quali[LapTimeSec], 2, BLANK())
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


## DAX for Race Dataset

### 1. Average Pace
The following DAX expression calculates average pace for each driver
```DAX
AvgPace = CALCULATE(
    AVERAGE(race[LapTimeSec]),
    ALLEXCEPT('race', race[Driver])
)   
```

### 2. Best Average Pace
The following DAX expression calculates best average pace 
```DAX
BestAvgLapTime = MINX(race, race[AvgPace])
```

### 3. Average Pace Delta
The following DAX expression calculates delta for average pace for each driver
```DAX
AvgPaceDelta = race[AvgPace] - race[BestAvgLapTime] 
```

### 4. Leading?
The following DAX expression checks if driver was on lead or not 
```DAX
Leading = IF(race[Position]=1, TRUE(), FALSE())
```

### 5. Pit Stop Number
The following DAX expression calculates how many times each driver was in pits
```DAX
PitStopCountPerDriver = 
CALCULATE(
    COUNTROWS(race),
    NOT(ISBLANK(race[PitInTimeSec])),
    ALLEXCEPT(race, race[Driver])
)
```

### 6. Positions At The Finish
The following DAX expression outputs positions at the finish for each driver
```DAX
PositionsAtTheFinish =
IF(race[LapNumber] = 53, race[Position],
 IF(race[LapNumber]=52, race[Position], BLANK())
)
```

### 7. Best Lap Time for Each Driver
The following DAX expression calculates best lap time for each driver
```DAX
BestLapTimePerDriver = 
CALCULATE(
    MINX(
        FILTER(
            'race',
            'race'[LapTimeSec] > 0),
        'race'[LapTimeSec]),
    ALLEXCEPT('race', 'race'[Driver])
)
```

### 8. Hards usage
The following DAX expression checks for hard tire for each driver
```DAX
HARDS = IF(race[Compound]="HARD", "HARD", BLANK())
```
The same DAX code was used for the rest of compounds accordingly.

### 9. Compound on The Start
The following DAX expression checks what compound was used at the start for each driver
```DAX
CompoundOnStart = IF(race[Stint]=1, race[Compound], BLANK())
```

### 10. Best Lap time Overall
The following DAX expression calculates best lap time of the race overall
```DAX
BestLapTimeOverall = MIN(race[BestLapTimePerDriver])
```

### 12. Delta for best lap time of each driver
The following DAX expression calculates delta for best lap time of each driver
```DAX
BestlapTimeDelta = race[BestLapTimePerDriver] - race[BestLapTimeOverall]
```

### 13. Best Lap Time Overall?
The following DAX expression outputs 1 if driver's best lap time = overall best lap time
```DAX
IsBestLapOverall = IF(race[LapTimeSec]=race[BestLapTimeOverall], 1, BLANK())
```

### 14. Top Speed Best?
The following DAX expression outputs 1 if driver's top speed = overall best top speed
```DAX
isTopSpeedBest = IF(race[SpeedST] = MAX(race[SpeedST]), 1, BLANK())
```

## Python Scripts in Power BI

### 1. Box plot showing the pace of each team.
```Python
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns


team_order = (dataset[["Team", "LapTimeSec"]].groupby("Team").median()["LapTimeSec"].sort_values().index)

team_palette = {
    'Red Bull Racing': '#00174C',
    'Ferrari': '#EF1A2D',
    'Mercedes': '#00A19B',
    'McLaren': '#FF8000',
    'Alpine': '#FF87BC',
    'Aston Martin': '#229971',
    'RB': '#6692FF',
    'Kick Sauber': '#52E252',
    'Williams': '#00A0DE',
    'Haas F1 Team': '#F9F2F2'
}

background_color = 'none' 

fig, ax = plt.subplots(figsize=(15, 10))
sns.boxplot(
    data=dataset,
    x='Team',
    y="LapTimeSec",
    hue="Team",
    whiskerprops=dict(color="white"),
    boxprops=dict(edgecolor="white"),
    medianprops=dict(color="grey"),
    capprops=dict(color="white"),
    palette=team_palette,
    order=team_order,
    showfliers=False
)

plt.xlabel("")
plt.ylabel('Lap Time in Sec', fontsize=15, color="white")
plt.grid(visible=False)
plt.xticks(rotation=45, color="white")
plt.yticks(color="white")
plt.title('Pace', fontsize=25, color='white')
fig.patch.set_facecolor(background_color)
ax.set_facecolor(background_color)
ax.spines['top'].set_color('white')
ax.spines['bottom'].set_color('white')
ax.spines['left'].set_color('white')
ax.spines['right'].set_color('white')



plt.show()
```
![boxPlot](assets/images/boxplot.png)

### 2. Scatter plot showing the correlation between tire life and lap times.
```Python
import matplotlib.pyplot as plt
import seaborn as sns


mean_lap_time = dataset["LapTimeSec"].mean()
std_lap_time = dataset["LapTimeSec"].std()

lower_bound = mean_lap_time - std_lap_time
upper_bound = mean_lap_time + std_lap_time

filtered_data = dataset[(dataset["LapTimeSec"] >= lower_bound) & (dataset["LapTimeSec"] <= upper_bound)]

palette_tires = {
    "HARD": "white",
    "MEDIUM": "yellow",
    "SOFT": "red"
}

background_color = 'none'

fig, ax = plt.subplots(figsize=(10, 5))
sns.scatterplot(
    data=filtered_data,  
    x="TyreLife",
    y="LapTimeSec",
    hue="Compound",
    palette=palette_tires,  
    ax=ax
)

plt.title("Lap Time vs. Tyre Life", color="white")
plt.xlabel("Tyre Life (in Laps)", color="white")
plt.ylabel("Lap Time", color="white")
plt.xticks(color="white")
plt.yticks(color="white")
fig.patch.set_facecolor(background_color)
ax.set_facecolor(background_color)
ax.spines['top'].set_color('white')
ax.spines['bottom'].set_color('white')
ax.spines['left'].set_color('white')
ax.spines['right'].set_color('white')

plt.show()
```
![ScatterPlot](assets/images/scatter.png)

### 3. Line plot showing position changes during the race.
```Python
import seaborn as sns
import matplotlib.pyplot as plt

driver_palette = {
    'VER': '#00174C',  
    'PER': '#00174C',  
    'LEC': '#EF1A2D',  
    'SAI': '#EF1A2D',  
    'HAM': '#00A19B',  
    'RUS': '#00A19B', 
    'NOR': '#FF8000',  
    'PIA': '#FF8000',  
    'OCO': '#FF87BC',  
    'GAS': '#FF87BC',  
    'ALO': '#229971', 
    'STR': '#229971',  
    'TSU': '#6692FF',  
    'RIC': '#6692FF',  
    'BOT': '#52E252',  
    'ZHO': '#52E252', 
    'ALB': '#00A0DE',
    'COL': '#00A0DE',  
    'MAG': '#F9F2F2',  
    'HUL': '#F9F2F2'   
}

line_styles = {
    'VER': 'solid',
    'PER': 'dashed',
    'LEC': 'solid',
    'SAI': 'dashed',
    'HAM': 'solid',
    'RUS': 'dashed',
    'NOR': 'solid',
    'PIA': 'dashed',
    'OCO': 'solid',
    'GAS': 'dashed',
    'ALO': 'solid',
    'STR': 'dashed',
    'TSU': 'solid',
    'RIC': 'dashed',
    'BOT': 'solid',
    'ZHO': 'dashed',
    'ALB': 'solid',
    'COL': 'dashed',
    'MAG': 'solid',
    'HUL': 'dashed'
}

background_color='none'

fig, ax = plt.subplots(figsize=(12, 6))
sns.lineplot(
    data=dataset,
    x="LapNumber",
    y="Position",
    hue="Driver",
    marker='o',
    palette=driver_palette,
    style="Driver",
    ax=ax
)

plt.title("Postions Over Time", color="white")
plt.xlabel("Lap Number", color="white")
plt.ylabel("Position", color="white")
plt.legend(title="Driver", bbox_to_anchor=(1, 1), loc='upper left')
ax.invert_yaxis()
ax.set_yticks(range(1, 21))  
ax.set_yticklabels(range(1, 21)) 
plt.xticks(color="white")
plt.yticks(color="white")
fig.patch.set_facecolor(background_color)
ax.set_facecolor(background_color)
ax.spines['top'].set_color('white')
ax.spines['bottom'].set_color('white')
ax.spines['left'].set_color('white')
ax.spines['right'].set_color('white')

plt.show()
```
![LinePlot](assets/images/line.png)


# Analysis

### 1. What were the qualifying and race results?

Qualifying was won by Lando Norris. Second place was claimed by his teammate Piastri, and third place went to Russell.

The race was won by Leclerc, with McLaren driver Piastri finishing in second place and Norris taking third.


### 2. What was the margin in qualifying?

The first five places remained within 0.186 seconds. After that, the gap to P1 began to grow more quickly, with the biggest gap reaching 2.118 seconds.

### 3. What was the performance of each driver in each sector?

The winner of the qualifying, Norris, had a poor sector 1 time, losing 0.269 seconds to Sainz. However, in the 2nd and 3rd sectors, Norris was the fastest driver and made up for the lost time.

We can also see that McLaren wasn't the best car in sector 1 because Norris's teammate Piastri also had quite a margin to Sainz. Sector 1 in Monza mostly consists of straights, which suggests that McLaren didn't have a fast car on the straights compared to rivals.

### 4. What were the top speeds in qualifying and the race?

The best top speed in qualifying was 353 km/h, achieved by Racing Bulls driver Daniel Ricciardo. In the race, the fastest top speed was 357 km/h, set by Haas F1 Team driver Kevin Magnussen.

It's worth to mention that slower teams had higher top speeds in both qualifying and the race. This could indicate that the top teams can afford to have slower cars on the straights because they can make up time in the corners.

Additionally, the higher top speed values in the race are largely due to slipstreaming and DRS.


### 5. What compounds did the drivers use in the race?
The most common compound was the hard tire, with drivers spending most of their time on it. Softs were almost not used at all; only Stroll drove 2 laps on this tire during the race. Probably to try and achieve the fastest lap of the race.


### 6. How many pit stops did each driver make?
|Driver|No. of pitstops|
|-----|----------------|
|LEC|1|
|PIA|2|
|NOR|2|
|SAI|1|
|HAM|2|
|VER|2|
|RUS|2|
|PER|2|
|MAG|1|
|ALB|1|

As we can see, the most common strategy among the top 10 was a two-stopper. However, the driver who won the race used a one-stop strategy, raising the question of whether the two-stop strategy was the best approach.


### 7. How did tire life affect pace?
For most drivers, lap times on medium tires started to drop after around 10 laps. On the other hand, the hard tires were able to maintain pace for over 30 laps.


### 8. What was the overall race pace for drivers and teams?
"It seems that McLaren had the fastest car, followed by Ferrari. Mercedes and Red Bull had very similar median lap times, and their lap times were more spread out. In the midfield, there were four teams, while Alpine and Kick Sauber had the slowest cars.


# Conclusion

The power of data in motorsport is invaluable. Teams can gather and analyze data to better understand their cars, identify strengths, and pinpoint areas for improvement. Historical data can also play a crucial role in informing race strategies and guiding setup changes. By analyzing past performances, teams can predict tire wear, fuel consumption, and even weather conditions, helping them make more informed decisions during a race. The ability to process and act on real-time data can be the difference between victory and defeat, making data analytics a key factor in modern motorsport success

# Appendix
[2024 Italian Grand Prix Poster](https://www.salracing.com/story/2024/8/2024-italian-grand-prix-posters)


