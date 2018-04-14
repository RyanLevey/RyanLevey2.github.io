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

However, as seen above, the dates are not in order. In order to counter this, the pd.to_datetime fucntion was used to convert the "date" column to the datetime format (so python can understand) and ordered by .sort_values function. This way, All the dates are in order.

```python
    retail['date'] = pd.to_datetime(retail['date'])
    retail = retail.sort_values(by='date')
    retail.set_index('date', inplace =True)
    retail = retail[retail['location']] == 'Utrecht'

```


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
