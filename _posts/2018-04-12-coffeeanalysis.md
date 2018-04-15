---
title: "Suburbia Assessment: The use of Alternative Data to Identify Purchasing Behavior"
date: 2018-04-12
tags: [data science, data analysis, data visualization]
header:
  image: "/images/Milan.jpg"
excerpt: "The Relationship between weather and value of coffee purchased"
---

# Company Profile: Suburbia

Suburbia is a start up based in Amsterdam that utilizes data from a variety of sources in effort to generate better insights for decision makers. Instead of using traditional sources such as industrial reports, and stock prices, Suburbia uses data from sources such as point of sale (POS), real estate, and a variety of other sources depending on the situation at hand.

For this assignment, the task was to develop an analysis for coffee sales (total product value sold) within the Utrecht area. The analysis could be conducted by comparing a variety of data relative to the euro amount of coffee sales. Because of the nature in the way coffee is marketed, companies are not only selling the coffee itself, but the ambiance associated with it. In effort to demonstrate this ambiance, coffee analyzed with the weather in the Utrecht.

# Step 1: Analyze/Import the Data  

For the analysis, Python along with its variety of libraries (as seen below) were used. With that being said, the following Python libraries were imported:

```python
    import pandas as pd
    import sklearn as sk
    import numpy as np
    import matplotlib.pyplot as plt
    import warnings
    warnings.filterwarnings('ignore')
    %matplotlib inline

```
Along with the data files:

```python
    weather_1 = pd.read_csv('...File Path...', delimiter = ',', encoding = 'utf-8-sig')
    retail = pd.read_csv('...File Path...', delimiter=',', encoding = 'utf-8-sig')

```

The .head() function was used to preview the data in effort to become familiar with the data features (aka: columns)


### Preview for weather:




```python
    weather_1.head()

```
<img src="{{ site.url }}{{ site.baseurl }}/images/weather_1head.JPG" alt="Final Data Frame For Coffee">

### Preview for retail sales:

```python
    retail.head()

```
<img src="{{ site.url }}{{ site.baseurl }}/images/retailhead.JPG" alt="Final Data Frame For Coffee">

After illustrating the features of the two data sets, it is then possible to identify which features are significant for the analysis and which are not. In this case, the most significant features are:

* total_transactions: The total transactions made within that day.
* appearances: The number of times the said good "name" appeared on the POS (Point of Sale).
* total_transaction_value: The total "Basket of Goods" bought on that day.
* total_product_value: The total value (or euro amount) of said good bought on that day.

After importing the libraries, data, and understanding the significance of the features, it is possible to begin "cleaning" the data.

# Step 2: Cleaning/Manipulating the Data

In order to analyze the two data frames, the two need to be aligned (aka: indexed) to provide better organization and transparency. Essentially, indexing the two data frames results in organizing the two data frames by a common feature (in this case dates).

As seen above, the date feature (column) for both data frames are not organized in any order. In order to organize the two, the pd.to_datetime function was used to convert the dates into datetime format and ordered by the .sort_values function.

```python
    retail['date'] = pd.to_datetime(retail['date'])
    retail = retail.sort_values(by='date')
    retail.set_index('date', inplace =True)
    retail = retail[retail['location']] == 'Utrecht'

```
After the data is sorted and organized, another element to be address is the high number of observations (rows) within the weather data set. Because we only have data from September 10th 2017 to March 1st 2017, the .loc function is used to filter weather dates within the timeline that we have for retail

```python
    weather_1U_retail = weather_1U_retail.loc['2017-09-10':'2018-08-01',:]
    weather_1U_retail.head()


```
### Weather Data Frame (Organized by date)
<img src="{{ site.url }}{{ site.baseurl }}/images/weather_1Uloc.JPG" alt="">

Since the two data frames are aligned, it is now possible to clean the retail data frame in further detail. As seen above, there are two types of coffees that are being observed, Expensive and Average coffee. Because this analysis involves coffee in relation to the weather, we must put the two observations (Expensive and  average coffee) together.

## First: Make two data frames for Expensive, and Average coffee. Then drop columns not being used

```python
# Put Average and Expensive coffee together in effort to understandthe total amount of coffee being consumed.

retail_ECoffee = retail[retail['name'] == 'Expensive coffee' ]
retail_ACoffee = retail[retail['name'] == 'Average coffee' ]
###### Edit the data frame
retail_ECoffee = retail_ECoffee.drop('_id__$oid',1)
retail_ECoffee = retail_ECoffee.drop('name',1)
retail_ECoffee = retail_ECoffee.drop('location',1)
retail_ACoffee = retail_ACoffee.drop('_id__$oid',1)
retail_ACoffee = retail_ACoffee.drop('name',1)
retail_ACoffee = retail_ACoffee.drop('location',1)

retail_ECoffee = retail_ECoffee.rename(columns={'total_transactions':'E_total_transactions,',
                                           'appearances': 'E_appearances',
                                           'total_transaction_value': 'E_total_transaction_value',
                                           'total_product_value':'E_product_value'})

retail_ACoffee = retail_ACoffee.rename(columns={'total_transactions':'A_total_transactions,',
                                           'appearances': 'A_appearances',
                                           'total_transaction_value': 'A_total_transaction_value',
                                           'total_product_value':'A_product_value'})

# Put the two data frames together
rTotalCoffee = retail_ECoffee.join(retail_ACoffee, how='outer')


rTotalCoffee.head()


```

## Second: From the new data frame (rTotalCoffee), Make three columns for total transactions, appearances, transaction value, and total product value. Then drop Expensive and Average coffee columns

```python
rTotalCoffee['t_total_transaction'] = rTotalCoffee['E_total_transactions,'] + rTotalCoffee['A_total_transactions,']
rTotalCoffee['t_appearances'] = rTotalCoffee['E_appearances'] + rTotalCoffee['A_appearances']
rTotalCoffee['t_total_transaction_value'] = rTotalCoffee['E_total_transaction_value'] + rTotalCoffee['A_total_transaction_value']
rTotalCoffee['t_total_product_value'] = rTotalCoffee['E_product_value'] + rTotalCoffee['E_product_value']

rFinalCoffee = rTotalCoffee.drop(rTotalCoffee.columns[[0, 1, 2, 3, 4, 5, 6, 7]], axis=1)

rFinalCoffee.head()

```

Final Data Frame for Coffee
<img src="{{ site.url }}{{ site.baseurl }}/images/rFinalCoffeedf.JPG" alt="Final Data Frame For Coffee">

## Third: Put the Weather and Total Coffee data together(and drop holidays)

```python
final = weather_1U_retail.join(rFinalCoffee, how='outer')
final.dropna(how='any')

```
With the clean data, it is possible to further manipulate the data to illustrate what weather consumers experienced on a particular day. More specifically, a logic needs to be develop in order to define what is a cold/mild day or a clear/cloudy day. This can be conducted by generating four new columns (coldclear, coldcloudy, mildclear, mildcloudy) which Boolean values (True/False, 1 and 0) are assigned in each column based upon the conditions of other features such as feelsLikeC and cloud cover.

In effort to effectively illustrate the “ambiance” that the consumer was experiencing, the feelsLikeC and cloudcover features (columns) were used. Yet again, the goal of this analysis is to determine what experience (ambiance) that encourages consumers to spend money on coffee.

```python
#Create Dummy Variables for in order to provide classification

final['coldclear'] = (final['feelsLikeC'] <= 9) & (final['cloudcover'] <= 49)
final['coldcloudy'] = (final['feelsLikeC'] <= 9) & (final['cloudcover'] >= 50)
final['mildclear'] = (final['feelsLikeC'] >= 10) & (final['cloudcover'] <= 49)
final['mildcloudy'] = (final['feelsLikeC'] >= 10) & (final['cloudcover'] >= 50)

#Convert to boolean values to int

final.coldclear = final.coldclear.astype(int)
final.coldcloudy = final.coldcloudy.astype(int)
final.mildclear = final.mildclear.astype(int)
final.mildcloudy = final.mildcloudy.astype(int)

```
### Dummy Variables
<img src="{{ site.url }}{{ site.baseurl }}/images/DummyVar1.JPG" alt="">

Lastly, the next objective is to combine the four weather columns into a single column. To do so, the Boolean values must be differentiated. This was done by assigning different integers to different types of weather. After assigning each weather type an integer, all features (columns) were added to the weather column and converted to strings for transparency.

```python

#Differentiate Boolean values
inal.coldcloudy[final.coldcloudy>0]+=1
final.mildclear[final.mildclear>0]+=2
final.mildcloudy[final.mildcloudy>0]+=3

final['weather'] = final['coldclear'] + final['coldcloudy'] + final['mildclear'] + final['mildcloudy']

#Assing integers to string value
final.weather[final.weather==1] = 'cold & clear'
final.weather[final.weather==2]= 'cold & cloudy'
final.weather[final.weather==3]= 'mild & clear'
final.weather[final.weather==4]= 'mild & cloudy'

```
### Final Data Frame for Analysis
<img src="{{ site.url }}{{ site.baseurl }}/images/finaldf.JPG" alt="">

# Step 3: Begin the analysis

After cleaning/manipulating the data, it is possible to identify potential consumer behaviors in relation to the weather.

## First: Conduct a Linear analysis

A linear plot is effective in identifying correlations between two variables. For this analysis, the total value of coffee along with temperature (feelsLikeC) and cloud coverage (cloudcover) were plotted.

```python
sb.pairplot(final, x_vars=['feelsLikeC'], y_vars='t_total_product_value', size=7,
            aspect=0.7, kind='reg' )
plt.title('Coffee Purchased(Product Value) & Temperature(Feels Like C)')


```

<img src="{{ site.url }}{{ site.baseurl }}/images/CoffePurchasedTempLin.JPG" alt="Final Data Frame For Coffee">

With the linear analysis, it is possible to identify that there is a negative correlation with temperature and total value of coffee purchased and a slightly positive correlation with respect to cloud coverage. However, due to the high variance of data, it is not possible to draw an effective analysis solely on these two plots alone.

## Second: Plot the data by group

Because we indexed (organized) the data points by date, each plot is particular due to the different weather observations. Therefor, each plotted point on the within the above analysis should be treated as so. In the illustration below, each data point is characterized by the type of weather consumers experienced on that particular day in relation to the total value of coffee they purchased.

<img src="{{ site.url }}{{ site.baseurl }}/images/ClusterWeatherCoffee.JPG" alt="Final Data Frame For Coffee">
