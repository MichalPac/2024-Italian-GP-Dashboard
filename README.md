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

