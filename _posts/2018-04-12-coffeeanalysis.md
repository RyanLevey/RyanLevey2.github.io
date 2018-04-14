---
title: "Suburbia Assessment: The use of Alternative Data to Identify Purchasing Behavior"
date: 2018-04-12
tags: [data science, data analysis, data visualization]
header:
  image: "/images/Milan.jpg"
excerpt: "The Relationship between weather and value of coffee purchased"
---

# Company Profile: Suburbia

Suburbia is a start up based in Amsterdam that utilizes data from a variety of source in order to develop rich insights for decision makers and investors. For example, instead of using traditional sources such as government/industrial reports, and stock prices, Suburbia uses data from sources such as sensor data from IOT (Internet of Things) devices, and POS (Point of Sale) data from Retail.

For my assessment, I was given data from sources such as the weather, retail sales (for multiple goods), housing prices in The Netherlands, and stock prices. I was asked to conduct an analysis of all of this data and develop a common theme.

# Step 1: Analyze/Import the Data  

As with the begining of every analysis, the first step is to import the Python libraries and data in order to have a quick analysis of it. This is in effort to become more familiar with the data and understand what features I am working with.

```python
    import pandas as pd
    import sklearn as sk
    import numpy as np
    import matplotlib.pyplot as plt
    import warnings
    warnings.filterwarnings('ignore')
    %matplotlib inline

```
And for the data files:

```python
    weather_1 = pd.read_csv('...File Path...', delimiter = ',', encoding = 'utf-8-sig')
    retail = pd.read_csv('...File Path...', delimiter=',', encoding = 'utf-8-sig')

```

Preview for weather:

```python
    weather_1.head()

```
<img src={{ site.url }}{{ site.baseurl }}/assets/images/filename.jpg" alt="">

Preview for retail sales:

```python
    retail.head()

```
<img src={{ site.url }}{{ site.baseurl }}/assets/images/filename.jpg" alt="">

Now we know what features are present within the two data sets, it is also importing to understand the significants of them as well (specifically for the retail data):
* total_transactions: The total transactions made within that day.
* appearances: The number of times the said good "name" appeared on the POS (Point of Sale).
* total_transaction_value: The total "Basket of Goods" bought on that day.
* total_product_value: The total value (or euro amount) of said good bought on that day.

After importing the libraries, data, and understanding the significance of the features, it is possible to begin "cleaning" the data.

# Step 2: Cleaning/Manipulating the Data

Since the two data sets have a common feature of the date, it is possible to utilize it for our index in order to better organize & align the two data sets.

However, as seen above, the dates are not in order. In order to counter this, the pd.to_datetime function was used to convert the "date" column to the datetime format (so python can understand) and ordered by .sort_values function. This way, All the dates are in order. Please note the same code was used for the weather_1 dataset as well.

```python
    retail['date'] = pd.to_datetime(retail['date'])
    retail = retail.sort_values(by='date')
    retail.set_index('date', inplace =True)
    retail = retail[retail['location']] == 'Utrecht'

```
By converting/ordering the dates, we can use the date as an index to provide better organization. However, there are more observations (rows) within the weather_1 data set versus the retail. In this case, we don't need all of the weather data, we just need the weather data from dates September 10th (2017-09-10) to March 1st (2018-03-01).

```python
    weather_1U_retail = weather_1U_retail.loc['2017-09-10':'2018-08-01',:]
    weather_1U_retail.head()


```
<img src={{ site.url }}{{ site.baseurl }}/assets/images/filename.jpg" alt="">

Now the two data frames span within the same dates. It is possible to continue to clean the retail data frame. The reason why this needs clean is because within the column "names", we have expensive, and average coffee that we are observing. With that being said, we need to put the two together and make a new data frame from it.

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
Now that the data is clean, we can manipulate it to understand what the weather was like for the given set of days. More specifically, we need to manipulate the data in such a manner that Python can understand if it was a warm/cold day or a clear/cloudy day. We can do this by creating four new columns (coldclear, coldcloudy, mildclear, mildcloudy) and give boolean values on each column based on the conditions of the feelsLikeC, and cloudcover columns.

Note: The reason why I used feelsLikeC for temperature is because for this analysis we want to understand what consumers are experiencing. The feelsLikeC temperature can better illustrate the overall experience and ambiance that consumers are experiencing. I also took cloudcover into consideration as well because it adds to the ambiance effect that I was trying to illustrate within my analysis.

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
<img src={{ site.url }}{{ site.baseurl }}/assets/images/filename.jpg" alt="">

The next objective combine the four weather types columns into a single column. To do so, each weather type needs to be assinged to an integer. For example, cold & clear weather will be assinged to 1 and cold & cloudy will be assinged to 2.

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
# Step 3: Begin the analysis

After cleaning/manipulating the data, it is possible to dive in to the analysis and identify potential consumer behaviors in relation to the weather.

## First: Conduct a Linear analysis

By doing a linear analysis, we are able to identify a correlation between two variables. In this case, it will be with total value of coffee along with temperature (feelsLikeC) and cloud coverage (cloudcover)

By using the seaborn library for visualization, we get the following results below.

```python
sb.pairplot(final, x_vars=['feelsLikeC'], y_vars='t_total_product_value', size=7,
            aspect=0.7, kind='reg' )
plt.title('Coffee Purchased(Product Value) & Temperature(Feels Like C)')


```

<img src="{{ site.url }}{{ site.baseurl }}/images/CoffePurchasedTempLin.JPG" alt="Final Data Frame For Coffee"> <img src="{{ site.url }}{{ site.baseurl }}/images/CoffeePurchasedCloudCoverageLin.JPG" alt="Final Data Frame For Coffee">


For cloud coverage:

<img src="{{ site.url }}{{ site.baseurl }}/images/CoffeePurchasedCloudCoverageLin.JPG" alt="Final Data Frame For Coffee">

## H2 Heading

### H3 Heading

Basic text.

some *italics*

some **bold**

A [link](https:jffj)

Bulleted list:
* First
+ Second
- Third

Numbered list:
1. First
2. Second
3. Third

Python code block:
```python
    import numpy as np

    def test_function(x,y):
      z = np.sum(x,y)
      return z
```
