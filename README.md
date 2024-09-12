

# Table of Contents
1. [Introduction](#introduction)
2. [Libraries](#libraries)
3. [Loading Data](#loading-data)
4. [Data Exploration](#data-exploration)
5. [Data Preprocessing](#data-preprocessing)
6. [Handling Duplicates](#handling-duplicates)
7. [Checking Data Types](#checking-data-types)
8. [Handling Missing Values](#handling-missing-values)
9. [Date and Time Conversion](#date-and-time-conversion)
10. [Analyzing Pickup Data](#analyzing-pickup-data)
11. [Analyzing Rush Hours](#analyzing-rush-hours)
12. [Combining Data Files](#combining-data-files)
13. [Visualizing Rush Locations](#visualizing-rush-locations)
14. [Examining Rush Hours](#examining-rush-hours)
15. [Results](#results)

## Introduction
This documentation provides a detailed guide to preprocess, clean, and analyze Uber trip data using Python. The process involves loading the data, exploring the data, handling duplicates, checking data types, handling missing values, converting date and time columns, analyzing pickup data, combining data files, visualizing rush locations, and examining rush hours.

**Author: Nihar Raju**

## Libraries
The following libraries are used in this code:
- `pandas`: For data manipulation and analysis.
- `numpy`: For numerical operations.
- `seaborn`: For statistical data visualization.
- `matplotlib.pyplot`: For plotting.
- `chart_studio.plotly`: For interactive plotting.
- `plotly.graph_objs`: For creating plotly graphs.
- `plotly.express`: For creating expressive plots.
- `folium`: For creating interactive maps.
- `os`: For file path manipulation.

```python
import pandas as pd
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt
import chart_studio.plotly as py
import plotly.graph_objs as go
import plotly.express as px
import folium

from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot
import os
```

## Loading Data
The data is loaded from a CSV file containing Uber trip data.

```python
# List all files in the directory
os.listdir(r"N:\datasets\Datasets")

# Load the data
df = pd.read_csv(r"N:\datasets\Datasets/uber-raw-data-janjune-15_sample.csv")

# Display the DataFrame
df

# Display the shape of the DataFrame
df.shape
```

## Data Exploration
The data is explored to understand its structure and check for duplicates.

```python
# Check for duplicates
df.duplicated
df.duplicated()

# Count the total number of duplicate rows
df.duplicated().sum()
```

## Data Preprocessing
The data is preprocessed by removing duplicates.

```python
# Drop duplicates
df.drop_duplicates(inplace=True)

# Create a copy of the DataFrame
df1 = df.copy()

# Drop duplicates in the copied DataFrame
df1.drop_duplicates(inplace=True)

# Display the shape of the DataFrame to verify that duplicates are removed
df1.shape

# Display the DataFrame
df1
```

## Handling Duplicates
Duplicates are handled by dropping them from the DataFrame.

```python
# Check for duplicates
df1.duplicated().sum()
```

## Checking Data Types
The data types of the columns are checked to ensure they are in the correct format.

```python
# Display the data types of the columns
df1.dtypes
```

## Handling Missing Values
The data is checked for missing values.

```python
# Check for missing values
df1.isnull().sum()
df1.isnull()
```

## Date and Time Conversion
The date and time columns are converted to the appropriate format.

```python
# Check the first value of the 'Pickup_date' column
df1['Pickup_date'][0]

# Check the type of the first value of the 'Pickup_date' column
type(df1['Pickup_date'][0])

# Convert the 'Pickup_date' column to datetime format
df1['Pickup_date'] = pd.to_datetime(df1['Pickup_date'])

# Display the data type of the 'Pickup_date' column
df1['Pickup_date'].dtype

# Display the first value of the 'Pickup_date' column after conversion
df1['Pickup_date'][0]

# Check the type of the first value of the 'Pickup_date' column after conversion
type(df1['Pickup_date'][0])
```

## Analyzing Pickup Data
The data is analyzed to determine the month with the highest number of Uber pickups in New York.

```python
# Extract the month from the 'Pickup_date' column
df1['month'] = df1['Pickup_date'].dt.month_name()

# Display the value counts of the 'month' column
df1['month'].value_counts()

# Plot the value counts of the 'month' column
df1['month'].value_counts().plot(kind='bar')
```

## Analyzing Rush Hours
The data is analyzed to determine the rush hours in New York.

```python
# Extract the day of the week from the 'Pickup_date' column
df1['Day_of_Week'] = df1['Pickup_date'].dt.day_name()

# Create a pivot table to analyze the rush hours
pivot = pd.crosstab(index=df1['month'], columns=df1['Day_of_Week'])

# Plot the pivot table
pivot.plot(kind='bar', figsize=(8, 6))
pivot.plot(kind='line', figsize=(8, 6))

# Extract the hour from the 'Pickup_date' column
df1['hour'] = df1['Pickup_date'].dt.hour

# Group the data by 'Day_of_Week' and 'hour' and calculate the size
summary = df1.groupby(['Day_of_Week', 'hour'], as_index=False).size()

# Plot the summary data
plt.figure(figsize=(8, 6))
sns.pointplot(x="hour", y="size", hue="Day_of_Week", data=summary)
plt.show()
```

## Combining Data Files
Additional data files are combined into a single DataFrame.

```python
# List all CSV files in the directory
files = os.listdir(r"N:\datasets\Datasets")[-9:-1]

# Remove specific files from the list
files.remove('uber-raw-data-janjune-15.csv')
files.remove('uber-raw-data-janjune-15_sample.csv')

# Initialize an empty DataFrame to store all data
final_df = pd.DataFrame()

# Iterate over each file and concatenate the data
for file in files:
    current_df = pd.read_csv(path + '/' + file)
    final_df = pd.concat([current_df, final_df])

# Display the shape of the final concatenated DataFrame
final_df.shape

# Check for duplicate rows and drop them
final_df.duplicated().sum()
final_df.drop_duplicates(inplace=True)
final_df.shape
```

## Visualizing Rush Locations
The rush locations in New York are visualized using Folium.

```python
# Group the data by 'Lat' and 'Lon' and calculate the size
rush_uber = final_df.groupby(['Lat', 'Lon'], as_index=False).size()

# Create a Folium map
basemap = folium.Map()

# Add a heatmap to the Folium map
HeatMap(rush_uber).add_to(basemap)
basemap
```

## Examining Rush Hours
The rush hours are examined by performing pairwise analysis.

```python
# Convert the 'Date/Time' column to datetime format
final_df['Date/Time'] = pd.to_datetime(final_df['Date/Time'], format="%m/%d/%Y %H:%M:%S")

# Extract the day and hour from the 'Date/Time' column
final_df['day'] = final_df['Date/Time'].dt.day
final_df['hour'] = final_df['Date/Time'].dt.hour

# Group the data by 'day' and 'hour' and calculate the size
pivot1 = final_df.groupby(['day', 'hour']).size().unstack()

# Display the pivot table with a background gradient
pivot1.style.background_gradient()

# Define a function to create a pivot table with a background gradient
def get_pivot_table(df, col1, col2):
    pivot1 = final_df.groupby([col1, col2]).size().unstack()
    return pivot1.style.background_gradient()

# Call the function to create a pivot table
get_pivot_table(final_df, "day", "hour")
```

## Results
The results include the cleaned DataFrame with duplicates removed, data types checked, missing values handled, date and time columns converted, pickup data analyzed, rush hours analyzed, data files combined, rush locations visualized, and rush hours examined.

### Cleaned DataFrame
- **Shape**: The shape of the DataFrame after removing duplicates.
- **Data Types**: The data types of the columns.
- **Missing Values**: The number of missing values in each column.
- **Date and Time Conversion**: The 'Pickup_date' column converted to datetime format.

### Pickup Data Analysis
- **Month with Highest Pickups**: The month with the highest number of Uber pickups in New York.

### Rush Hours Analysis
- **Rush Hours**: The rush hours in New York.

### Combined Data Files
- **Final DataFrame**: The final concatenated DataFrame with duplicates removed.

### Rush Locations Visualization
- **Heatmap**: A heatmap showing the rush locations in New York.

### Rush Hours Examination
- **Pivot Table**: A pivot table showing the rush hours in New York with a background gradient.



**Author: Nihar Raju**
