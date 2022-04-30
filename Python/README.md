# Data Processing in Python
#### Data preprocessing is the first step in data visualization, data analytics, and machine learning, where data is prepared for analytics functions to get the best possible insights. 

* To use different technical and analytical aspects of data preprocessing – data collection, data cleaning, data integration, data reduction, and data transformation and get to grips with implementing them using the open source Python programming environment, lead to more effective decision making. 
* To use Python to read, manipulate, and analyze data; perform data cleaning, integration, reduction, and transformation techniques; and handle outliers or missing values to effectively prepare data for analytic tools.

### Data Wrangling by Pandas <a href=https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html>Python Pandas's DataFrame API</a>

* Data Preview
  * Methods to check related infomration, exmaples:
<code>import numpy as np</code><br>
<code>import pandas as pd</code><br>
<code>df = pd.DataFrame()</code><br>
<code>df.info()</code> or <code>df.describe()</code><br>
<code>head(), tail(), sample(), len(), count()</code><br>
<code>df.dtypes, df.columns, df.shape</code><br>

* Missing and Duplicate Values
  * To check missing values
<code>df.isnull(), df.isnull().any(), df.isnull().sum()</code>
  * To process missing values
<code>df.dropna() ，df.fillna()</code>
  * To check or delete duplicated values
<code>df.duplicated(), df.drop_duplicates()</code>
