# Data-Analysis-for-Power-Outages
this is a project for DSC 80 at UCSD


# Project03: Analysis for outage

**Name(s)**: Zhiheng Wu

**Website Link**: https://engineering.purdue.edu/LASCI/research-data/outages

## Code


```python
# Some Codes are helped by GPT, because either they are too trivial to settle, or too easy to manually handle.
```


```python
import pandas as pd
import numpy as np
import os

import plotly.express as px
pd.options.plotting.backend = 'plotly'
```


```python
import requests
from bs4 import BeautifulSoup
```


```python
# Question:
# From the characteristics of outage and the trends between variables,
# how can the municipal department improve?
```

### Cleaning and EDA


```python
# TODO

file_path = '/Users/zhihengwu/zw/Academics/dsc80-fall2023/dsc80-2023-fa/projects/03-topic/outage.xlsx'

data = pd.read_excel(file_path, skiprows=5, usecols='B:BE')[1:].drop('OBS', axis = 1)
data_cleaned = data.copy()
data_cleaned['YEAR'] = pd.to_numeric(data_cleaned['YEAR'], errors='coerce').astype('Int64')
data_cleaned['MONTH'] = pd.to_numeric(data_cleaned['MONTH'], errors='coerce').astype('Int64')
data_cleaned
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>YEAR</th>
      <th>MONTH</th>
      <th>U.S._STATE</th>
      <th>POSTAL.CODE</th>
      <th>NERC.REGION</th>
      <th>CLIMATE.REGION</th>
      <th>ANOMALY.LEVEL</th>
      <th>CLIMATE.CATEGORY</th>
      <th>OUTAGE.START.DATE</th>
      <th>OUTAGE.START.TIME</th>
      <th>...</th>
      <th>POPPCT_URBAN</th>
      <th>POPPCT_UC</th>
      <th>POPDEN_URBAN</th>
      <th>POPDEN_UC</th>
      <th>POPDEN_RURAL</th>
      <th>AREAPCT_URBAN</th>
      <th>AREAPCT_UC</th>
      <th>PCT_LAND</th>
      <th>PCT_WATER_TOT</th>
      <th>PCT_WATER_INLAND</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>2011</td>
      <td>7</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.3</td>
      <td>normal</td>
      <td>2011-07-01 00:00:00</td>
      <td>17:00:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2014</td>
      <td>5</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2014-05-11 00:00:00</td>
      <td>18:38:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2010</td>
      <td>10</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-1.5</td>
      <td>cold</td>
      <td>2010-10-26 00:00:00</td>
      <td>20:00:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2012</td>
      <td>6</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2012-06-19 00:00:00</td>
      <td>04:30:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>5</th>
      <td>2015</td>
      <td>7</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>1.2</td>
      <td>warm</td>
      <td>2015-07-18 00:00:00</td>
      <td>02:00:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1530</th>
      <td>2011</td>
      <td>12</td>
      <td>North Dakota</td>
      <td>ND</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>-0.9</td>
      <td>cold</td>
      <td>2011-12-06 00:00:00</td>
      <td>08:00:00</td>
      <td>...</td>
      <td>59.9</td>
      <td>19.9</td>
      <td>2192.2</td>
      <td>1868.2</td>
      <td>3.9</td>
      <td>0.27</td>
      <td>0.1</td>
      <td>97.599649</td>
      <td>2.401765</td>
      <td>2.401765</td>
    </tr>
    <tr>
      <th>1531</th>
      <td>2006</td>
      <td>&lt;NA&gt;</td>
      <td>North Dakota</td>
      <td>ND</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>59.9</td>
      <td>19.9</td>
      <td>2192.2</td>
      <td>1868.2</td>
      <td>3.9</td>
      <td>0.27</td>
      <td>0.1</td>
      <td>97.599649</td>
      <td>2.401765</td>
      <td>2.401765</td>
    </tr>
    <tr>
      <th>1532</th>
      <td>2009</td>
      <td>8</td>
      <td>South Dakota</td>
      <td>SD</td>
      <td>RFC</td>
      <td>West North Central</td>
      <td>0.5</td>
      <td>warm</td>
      <td>2009-08-29 00:00:00</td>
      <td>22:54:00</td>
      <td>...</td>
      <td>56.65</td>
      <td>26.73</td>
      <td>2038.3</td>
      <td>1905.4</td>
      <td>4.7</td>
      <td>0.3</td>
      <td>0.15</td>
      <td>98.307744</td>
      <td>1.692256</td>
      <td>1.692256</td>
    </tr>
    <tr>
      <th>1533</th>
      <td>2009</td>
      <td>8</td>
      <td>South Dakota</td>
      <td>SD</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>0.5</td>
      <td>warm</td>
      <td>2009-08-29 00:00:00</td>
      <td>11:00:00</td>
      <td>...</td>
      <td>56.65</td>
      <td>26.73</td>
      <td>2038.3</td>
      <td>1905.4</td>
      <td>4.7</td>
      <td>0.3</td>
      <td>0.15</td>
      <td>98.307744</td>
      <td>1.692256</td>
      <td>1.692256</td>
    </tr>
    <tr>
      <th>1534</th>
      <td>2000</td>
      <td>&lt;NA&gt;</td>
      <td>Alaska</td>
      <td>AK</td>
      <td>ASCC</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>66.02</td>
      <td>21.56</td>
      <td>1802.6</td>
      <td>1276</td>
      <td>0.4</td>
      <td>0.05</td>
      <td>0.02</td>
      <td>85.761154</td>
      <td>14.238846</td>
      <td>2.901182</td>
    </tr>
  </tbody>
</table>
<p>1534 rows × 55 columns</p>
</div>




```python
data_cleaned = data_cleaned.drop(data_cleaned.index[0])

data_cleaned.reset_index(drop=True, inplace=True)

missing_data = data_cleaned.isnull().sum()

missing_data[missing_data > 0]
```




    MONTH                         9
    CLIMATE.REGION                6
    ANOMALY.LEVEL                 9
    CLIMATE.CATEGORY              9
    OUTAGE.START.DATE             9
    OUTAGE.START.TIME             9
    OUTAGE.RESTORATION.DATE      58
    OUTAGE.RESTORATION.TIME      58
    CAUSE.CATEGORY.DETAIL       470
    HURRICANE.NAMES            1461
    OUTAGE.DURATION              58
    DEMAND.LOSS.MW              704
    CUSTOMERS.AFFECTED          443
    RES.PRICE                    22
    COM.PRICE                    22
    IND.PRICE                    22
    TOTAL.PRICE                  22
    RES.SALES                    22
    COM.SALES                    22
    IND.SALES                    22
    TOTAL.SALES                  22
    RES.PERCEN                   22
    COM.PERCEN                   22
    IND.PERCEN                   22
    POPDEN_UC                    10
    POPDEN_RURAL                 10
    dtype: int64




```python
data_cleaned
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>YEAR</th>
      <th>MONTH</th>
      <th>U.S._STATE</th>
      <th>POSTAL.CODE</th>
      <th>NERC.REGION</th>
      <th>CLIMATE.REGION</th>
      <th>ANOMALY.LEVEL</th>
      <th>CLIMATE.CATEGORY</th>
      <th>OUTAGE.START.DATE</th>
      <th>OUTAGE.START.TIME</th>
      <th>...</th>
      <th>POPPCT_URBAN</th>
      <th>POPPCT_UC</th>
      <th>POPDEN_URBAN</th>
      <th>POPDEN_UC</th>
      <th>POPDEN_RURAL</th>
      <th>AREAPCT_URBAN</th>
      <th>AREAPCT_UC</th>
      <th>PCT_LAND</th>
      <th>PCT_WATER_TOT</th>
      <th>PCT_WATER_INLAND</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2014</td>
      <td>5</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2014-05-11 00:00:00</td>
      <td>18:38:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2010</td>
      <td>10</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-1.5</td>
      <td>cold</td>
      <td>2010-10-26 00:00:00</td>
      <td>20:00:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2012</td>
      <td>6</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-0.1</td>
      <td>normal</td>
      <td>2012-06-19 00:00:00</td>
      <td>04:30:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>3</th>
      <td>2015</td>
      <td>7</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>1.2</td>
      <td>warm</td>
      <td>2015-07-18 00:00:00</td>
      <td>02:00:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>4</th>
      <td>2010</td>
      <td>11</td>
      <td>Minnesota</td>
      <td>MN</td>
      <td>MRO</td>
      <td>East North Central</td>
      <td>-1.4</td>
      <td>cold</td>
      <td>2010-11-13 00:00:00</td>
      <td>15:00:00</td>
      <td>...</td>
      <td>73.27</td>
      <td>15.28</td>
      <td>2279</td>
      <td>1700.5</td>
      <td>18.2</td>
      <td>2.14</td>
      <td>0.6</td>
      <td>91.592666</td>
      <td>8.407334</td>
      <td>5.478743</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>1528</th>
      <td>2011</td>
      <td>12</td>
      <td>North Dakota</td>
      <td>ND</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>-0.9</td>
      <td>cold</td>
      <td>2011-12-06 00:00:00</td>
      <td>08:00:00</td>
      <td>...</td>
      <td>59.9</td>
      <td>19.9</td>
      <td>2192.2</td>
      <td>1868.2</td>
      <td>3.9</td>
      <td>0.27</td>
      <td>0.1</td>
      <td>97.599649</td>
      <td>2.401765</td>
      <td>2.401765</td>
    </tr>
    <tr>
      <th>1529</th>
      <td>2006</td>
      <td>&lt;NA&gt;</td>
      <td>North Dakota</td>
      <td>ND</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>59.9</td>
      <td>19.9</td>
      <td>2192.2</td>
      <td>1868.2</td>
      <td>3.9</td>
      <td>0.27</td>
      <td>0.1</td>
      <td>97.599649</td>
      <td>2.401765</td>
      <td>2.401765</td>
    </tr>
    <tr>
      <th>1530</th>
      <td>2009</td>
      <td>8</td>
      <td>South Dakota</td>
      <td>SD</td>
      <td>RFC</td>
      <td>West North Central</td>
      <td>0.5</td>
      <td>warm</td>
      <td>2009-08-29 00:00:00</td>
      <td>22:54:00</td>
      <td>...</td>
      <td>56.65</td>
      <td>26.73</td>
      <td>2038.3</td>
      <td>1905.4</td>
      <td>4.7</td>
      <td>0.3</td>
      <td>0.15</td>
      <td>98.307744</td>
      <td>1.692256</td>
      <td>1.692256</td>
    </tr>
    <tr>
      <th>1531</th>
      <td>2009</td>
      <td>8</td>
      <td>South Dakota</td>
      <td>SD</td>
      <td>MRO</td>
      <td>West North Central</td>
      <td>0.5</td>
      <td>warm</td>
      <td>2009-08-29 00:00:00</td>
      <td>11:00:00</td>
      <td>...</td>
      <td>56.65</td>
      <td>26.73</td>
      <td>2038.3</td>
      <td>1905.4</td>
      <td>4.7</td>
      <td>0.3</td>
      <td>0.15</td>
      <td>98.307744</td>
      <td>1.692256</td>
      <td>1.692256</td>
    </tr>
    <tr>
      <th>1532</th>
      <td>2000</td>
      <td>&lt;NA&gt;</td>
      <td>Alaska</td>
      <td>AK</td>
      <td>ASCC</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>NaN</td>
      <td>...</td>
      <td>66.02</td>
      <td>21.56</td>
      <td>1802.6</td>
      <td>1276</td>
      <td>0.4</td>
      <td>0.05</td>
      <td>0.02</td>
      <td>85.761154</td>
      <td>14.238846</td>
      <td>2.901182</td>
    </tr>
  </tbody>
</table>
<p>1533 rows × 55 columns</p>
</div>




```python
# univariate analysis
```


```python
import matplotlib.pyplot as plt

month_distribution = data_cleaned['MONTH'].dropna().astype(int).value_counts().sort_index()

anomaly_distribution = data_cleaned['ANOMALY.LEVEL'].dropna().astype(float)

customers_affected_distribution = data_cleaned['CUSTOMERS.AFFECTED'].dropna().astype(float)

fig, ax = plt.subplots(1, 3, figsize=(18, 5))

ax[0].bar(month_distribution.index, month_distribution.values, color='skyblue')
ax[0].set_title('Distribution of MONTH')
ax[0].set_xlabel('Month')
ax[0].set_ylabel('Frequency')

ax[1].hist(anomaly_distribution, bins=30, color='salmon')
ax[1].set_title('Distribution of ANOMALY.LEVEL')
ax[1].set_xlabel('Anomaly Level')
ax[1].set_ylabel('Frequency')

ax[2].hist(customers_affected_distribution, bins=30, color='lightgreen')
ax[2].set_title('Distribution of CUSTOMERS.AFFECTED')
ax[2].set_xlabel('Customers Affected (in millions)')
ax[2].set_ylabel('Frequency')

plt.tight_layout()
plt.show()
```


    
![png](outages_files/outages_11_0.png)
    



```python
# These three histograms are all analysis towards frequency, aiming at different variables.
# The first one can apparently show which month is most or least likely for outage, while the second one can
# show which anomaly level is most or least likely for outage, and the third graph can show which range of customer
# affected is most or least likely for outage.
```


```python
# bivariate analysis
```


```python
import seaborn as sns

# Function to create and display a scatter plot for two given columns
def plot_scatter(data, x_column, y_column, title, xlabel, ylabel):
    plt.figure(figsize=(10, 6))
    sns.scatterplot(data=data, x=x_column, y=y_column)
    plt.title(title)
    plt.xlabel(xlabel)
    plt.ylabel(ylabel)
    plt.show()

# Let's create a scatter plot for 'ANOMALY.LEVEL' and 'OUTAGE.DURATION'
plot_scatter(data_cleaned, 'ANOMALY.LEVEL', 'OUTAGE.DURATION', 
             'Scatter Plot of Outage Duration vs Anomaly Level', 
             'Anomaly Level', 'Outage Duration (minutes)')
```

    /Users/zhihengwu/anaconda3/lib/python3.11/site-packages/seaborn/_oldcore.py:1498: FutureWarning: is_categorical_dtype is deprecated and will be removed in a future version. Use isinstance(dtype, CategoricalDtype) instead
      if pd.api.types.is_categorical_dtype(vector):
    /Users/zhihengwu/anaconda3/lib/python3.11/site-packages/seaborn/_oldcore.py:1498: FutureWarning: is_categorical_dtype is deprecated and will be removed in a future version. Use isinstance(dtype, CategoricalDtype) instead
      if pd.api.types.is_categorical_dtype(vector):



    
![png](outages_files/outages_14_1.png)
    



```python
# The scatter plot shows the relationship between 'ANOMALY.LEVEL' and 'OUTAGE.DURATION'.
# From the plot, it shows not very clear visible trends
# or correlations between the severity of climate anomalies and the duration of power outages.
```


```python
# interesting aggregates
```


```python
data_cleaned['CUSTOMERS.AFFECTED'] = pd.to_numeric(data_cleaned['CUSTOMERS.AFFECTED'], errors='coerce')

average_outage_duration = data_cleaned.groupby('CLIMATE.REGION')['OUTAGE.DURATION'].mean().reset_index()

total_customers_affected = data_cleaned.groupby('U.S._STATE')['CUSTOMERS.AFFECTED'].sum().reset_index()

average_outage_duration, total_customers_affected
```




    (       CLIMATE.REGION OUTAGE.DURATION
     0             Central      2701.13089
     1  East North Central     5368.897059
     2           Northeast     2991.656977
     3           Northwest          1284.5
     4               South     2846.100917
     5           Southeast     2217.686667
     6           Southwest     1566.136364
     7                West     1628.331707
     8  West North Central        696.5625,
                   U.S._STATE  CUSTOMERS.AFFECTED
     0                Alabama            471644.0
     1                 Alaska             14273.0
     2                Arizona            579624.0
     3               Arkansas            619760.0
     4             California          25573446.0
     5               Colorado            451667.0
     6            Connecticut            784410.0
     7               Delaware             83400.0
     8   District of Columbia           1752383.0
     9                Florida          12732240.0
     10               Georgia           1930883.0
     11                Hawaii            736186.0
     12                 Idaho             35000.0
     13              Illinois           9109195.0
     14               Indiana           2364749.0
     15                  Iowa            376000.0
     16                Kansas            756000.0
     17              Kentucky           1305310.0
     18             Louisiana           3020062.0
     19                 Maine            932269.0
     20              Maryland           5424069.0
     21         Massachusetts           1169751.0
     22              Michigan          13759042.0
     23             Minnesota           1666092.0
     24           Mississippi             10000.0
     25              Missouri            657944.0
     26               Montana                 0.0
     27              Nebraska            261212.0
     28                Nevada            111100.0
     29         New Hampshire            152568.0
     30            New Jersey           4966721.0
     31            New Mexico            500000.0
     32              New York           8580414.0
     33        North Carolina           3586494.0
     34          North Dakota             34500.0
     35                  Ohio           4924174.0
     36              Oklahoma           3052981.0
     37                Oregon            527503.0
     38          Pennsylvania           8595378.0
     39        South Carolina           2015305.0
     40          South Dakota                 0.0
     41             Tennessee           1008395.0
     42                 Texas          20983817.0
     43                  Utah            225010.0
     44               Vermont                 0.0
     45              Virginia           5080588.0
     46            Washington           4485537.0
     47         West Virginia            539383.0
     48             Wisconsin            458760.0
     49               Wyoming             35500.0)




```python
# The above are two summary tables

# 1. The average outage duration for each climate region:
# For each climate region, you can see the mean duration of outages, 
# which helps to understand which regions may experience longer disruptions on average.

# conclusion 1: East North Central should pay great attention to the problems. Warning!

# 2. The total number of customers affected for each U.S. state:

# For each U.S. state, the total number of customers affected by outages is calculated, 
# which provides insight into the impact of outages at the state level.

# conclusion 2: Once California got an outage, it will lead to a greater amount of influence on the citizens.
# (because California itself has a huge population)

```


```python

```

### Assessment of Missingness


```python
# TODO
```


```python
# NMAR analysis
```


```python
# Take 'CUSTOMERS.AFFECTED'

# Imagine the data generating process. We can perform permutation tests to determine the dependency of
# the missingness of 'CUSTOMERS.AFFECTED'. And then, look at the observed test statistics
# to see whether it is NMAR.
```


```python
# I assume 'CUSTOMERS.AFFECTED' is not missing at random (NMAR).
```


```python
# But let's see:
```


```python
from scipy.stats import ttest_ind

outage_data = data_cleaned

outage_data['CUSTOMERS.AFFECTED_MISSING'] = outage_data['CUSTOMERS.AFFECTED'].isnull().astype(int)

grouped_customers = outage_data.groupby('CUSTOMERS.AFFECTED_MISSING')['ANOMALY.LEVEL']
observed_statistic_customers, _ = ttest_ind(grouped_customers.get_group(1), grouped_customers.get_group(0), nan_policy='omit')

permuted_statistics_customers = []

n_permutations = 1000

for _ in range(n_permutations):
    permuted_missingness_customers = outage_data['CUSTOMERS.AFFECTED_MISSING'].sample(frac=1, replace=False).reset_index(drop=True)
    grouped_customers = outage_data.groupby(permuted_missingness_customers)['ANOMALY.LEVEL']
    stat, _ = ttest_ind(grouped_customers.get_group(1), grouped_customers.get_group(0), nan_policy='omit')
    permuted_statistics_customers.append(stat)

p_value_dependency_customers = (np.abs(permuted_statistics_customers) >= np.abs(observed_statistic_customers)).mean()

observed_correlation_customers = outage_data[['MONTH', 'CUSTOMERS.AFFECTED_MISSING']].corr().iloc[0, 1]

permuted_correlations_customers = []


for _ in range(n_permutations):
    permuted_month_customers = outage_data['MONTH'].sample(frac=1, replace=False).reset_index(drop=True)
    corr = pd.concat([permuted_month_customers, outage_data['CUSTOMERS.AFFECTED_MISSING']], axis=1).corr().iloc[0, 1]
    permuted_correlations_customers.append(corr)

p_value_independence_customers = (np.abs(permuted_correlations_customers) >= np.abs(observed_correlation_customers)).mean()

(observed_statistic_customers, p_value_dependency_customers), (observed_correlation_customers, p_value_independence_customers)

```


    ---------------------------------------------------------------------------

    TypeError                                 Traceback (most recent call last)

    Cell In[22], line 11
          8 # Perform permutation test for dependency with 'ANOMALY.LEVEL'
          9 # First, calculate the observed test statistic (mean difference in 'ANOMALY.LEVEL')
         10 grouped_customers = outage_data.groupby('CUSTOMERS.AFFECTED_MISSING')['ANOMALY.LEVEL']
    ---> 11 observed_statistic_customers, _ = ttest_ind(grouped_customers.get_group(1), grouped_customers.get_group(0), nan_policy='omit')
         13 # Now, perform permutation test for dependency
         14 permuted_statistics_customers = []


    File ~/anaconda3/lib/python3.11/site-packages/scipy/stats/_axis_nan_policy.py:513, in _axis_nan_policy_factory.<locals>.axis_nan_policy_decorator.<locals>.axis_nan_policy_wrapper(***failed resolving arguments***)
        510 # Addresses nan_policy == "omit"
        511 if any(contains_nan) and nan_policy == 'omit':
        512     # consider passing in contains_nan
    --> 513     samples = _remove_nans(samples, paired)
        515 # ideally, this is what the behavior would be:
        516 # if is_too_small(samples):
        517 #     return tuple_to_result(np.nan, np.nan)
        518 # but some existing functions raise exceptions, and changing
        519 # behavior of those would break backward compatibility.
        521 if sentinel:


    File ~/anaconda3/lib/python3.11/site-packages/scipy/stats/_axis_nan_policy.py:142, in _remove_nans(samples, paired)
        140 # potential optimization: don't copy arrays that don't contain nans
        141 if not paired:
    --> 142     return [sample[~np.isnan(sample)] for sample in samples]
        144 # for paired samples, we need to remove the whole pair when any part
        145 # has a nan
        146 nans = np.isnan(samples[0])


    File ~/anaconda3/lib/python3.11/site-packages/scipy/stats/_axis_nan_policy.py:142, in <listcomp>(.0)
        140 # potential optimization: don't copy arrays that don't contain nans
        141 if not paired:
    --> 142     return [sample[~np.isnan(sample)] for sample in samples]
        144 # for paired samples, we need to remove the whole pair when any part
        145 # has a nan
        146 nans = np.isnan(samples[0])


    TypeError: ufunc 'isnan' not supported for the input types, and the inputs could not be safely coerced to any supported types according to the casting rule ''safe''



```python
# For 'CUSTOMERS.AFFECTED':

# 1. Dependency Test:
# The observed test statistic for the dependency of missingness on 'ANOMALY.LEVEL': 
observed_statistic_customers

# And the p-value:
p_value_dependency_customers
# which is less than 0.05 traditionally, therefore we regard 'CUSTOMERS.AFFECTED'
# as MAR because it depends on another column.

# 2. Independence Test:

# The observed correlation for the independence test with 'MONTH' is: 
observed_correlation_customers
# The p-value is:
p_value_independence_customers
# So, 'CUSTOMERS.AFFECTED' is independent on 'MONTH'.

```


```python

```

### Hypothesis Testing


```python
# TODO
```


```python
# Null Hypothesis (H0):
# There is no significant difference in the average number of customers affected by power outages
# between different climate conditions. (means are equal)

# Alternative Hypothesis (H1): 
# There is a significant difference in the average number of customers affected by power outages
# between different climate conditions. (means are not equal)
```


```python
# the below is helped by GPT
import scipy.stats as stats

anova_data = outage_data.dropna(subset=['CUSTOMERS.AFFECTED'])

group_sizes = anova_data.groupby('CLIMATE.REGION')['CUSTOMERS.AFFECTED'].size()
valid_groups = group_sizes[group_sizes > 1].index.tolist() 

anova_result_filtered = stats.f_oneway(
    *[anova_data['CUSTOMERS.AFFECTED'][anova_data['CLIMATE.REGION'] == region] for region in valid_groups]
)

f_statistic_filtered, p_value_filtered = anova_result_filtered

```


```python
f_statistic_filtered
```


```python
p_value_filtered
```


```python
# Because p-value is less than 0.05
# we reject the null hypothesis.
# So, there are exactly significant differences in the average number of customers affected by power outages
# between the different climate regions.
```


```python
# Conclusion for the whole project:
# We can find different outcomes affected by power outages between different regions. 
# Therefore, policymakers should lean forward resources to the climate regions where are more severely affected by 
# power outages, and where the region itself has a huge population so once it got affected then
# significant number of people would be affected.
```


```python
# Website: https://zwinthestudio.github.io/Data-Analysis-for-Power-Outages/
```
