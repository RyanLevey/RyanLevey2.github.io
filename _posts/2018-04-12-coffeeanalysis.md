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

As with the begining of every analysis, the first step is to import the Python libraries and data in order to have a quick analysis of it. This is in effort to become more familiar with the data and understand what features I am working with. W

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
