# Data Processing in Python
#### Data preprocessing is the first step in data visualization, data analytics, and machine learning, where data is prepared for analytics functions to get the best possible insights. 

* To use different technical and analytical aspects of data preprocessing – data collection, data cleaning, data integration, data reduction, and data transformation and get to grips with implementing them using the open source Python programming environment, lead to more effective decision making. 
* To use Python to read, manipulate, and analyze data; perform data cleaning, integration, reduction, and transformation techniques; and handle outliers or missing values to effectively prepare data for analytic tools.

### Data Wrangling by Pandas 
<a href=https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.html>Python Pandas's DataFrame API</a>

* Data Preview
  * Methods to check related infomration, exmaples:
<code>import numpy as np</code><br>
<code>import pandas as pd</code><br>
<code>df = pd.DataFrame()</code><br>
<code>df.info()</code> or <code>df.describe()</code><br>
<code>head(), tail(), sample(), len(), count()</code><br>
<code>df.dtypes, df.columns, df.shape</code><br>

* Missing and Duplicate Values
   * To check missing values by using <code>df.isnull(), df.isnull().any(), df.isnull().sum()</code>
   * To process missing values by using <code>df.dropna() ，df.fillna()</code>
   * To check or delete duplicated values by using <code>df.duplicated(), df.drop_duplicates()</code>
 
 * Manpulate data
   * To replace eata by <code>replace()</code> such as <code>df["No"].replace(r'BA.$', value='NEW', regex=True, inplace = True)</code><br>
   * To rank data by <code>rank()</code> such as <code>df["Ranking"] = df.rank(method="dense").astype("int")</code><br>
   * To truncate data by <code>clip()</code> such as df["Days"] = df["Days"].clip(0,31)<br>
   * To return unique data by <code>unique() and nunique()</code> such as df["gender"].unique()<br>
   * To process DataFrame by user defined function - <code>apply() or applymap()</code> such as <code>df["Number"].apply(lambda x: x+1)</code><br>
 
 * Manpulate string
   * <code>cat, contains, startswith/endswith, get, upper/lower, pad/center, repeat, slice_replace, split, strip/rstrip/lstrip, findall, extract/extractall</code>, examples: 
   * <code>df.insert(2, "Name", df["Last Name"].str.cat(df["First Name"], sep=""))</code>
   * <code>df["Phone"] = df["Phone"].str.slice_replace(3,7,"*"*4)</code>
   * <code>df["Address"].str.extract("([\u4e00-\u9fa5]+)")</code>
 
 * Manpulate Row/Column
   * To reset index after deleted NULL by <code>reset_index()</code> such as <code>df.reset_index(drop=True)</code>
   * To rename heading of column by <code>rename()</code> such as <code>df.rename(columns={'mark': 'sell'}, inplace=True)</code>
   * To roate row/column of DataFrame by <code>T</code> such as <code>df.T</code>
   * To drop row/column by <code>drop()</code> such as <code>df.drop(columns=["mark"])</code>
   * To convert wide table to long table (tabular data to tree data) by <code>melt()</code> such as <code>df.melt(id_vars="Name", var_name="Subject", value_name="Score")</code>
   * To turn a long table into a wide table (tree data into tabular data) by <code>pivot()</code> such as <code>df.pivot(index='Name', columns='Subject', values='Score')</code> or <code>df1.set_index(['Name','Subject']).unstack('Subject')</code>
   * To group and pivot Tables by <code>groupby() and pivot_table()</code> such as <code>df.groupby("Subject").mean()</code> and <code>df_long.pivot_table(index=["Student", "School"], columns='Class', values='Grade', margins=True, aggfunc='sum').reset_index()</code>

* Data filtering
  * To select column by <code>df[col]</code>
  * To select row with index by <code>df.loc[label] or df.iloc[loc]</code>
  * To slice DataFrame such as <code>df[:5]</code>
  * To filter row by expression <code>df[bool_vec]</code>
  * To fiter by <code>query()</code>
  * To tilter datatype by <code>select_dtypes()</code> such as <code>df.select_dtypes("int64")</code>
  * To verify values exist or not by <code>isin()</code> such as <code>name_list = ["Kevin", "Tom"] df[df["Name"].isin(name_list)]

* Statistical operations on numerical data
   * <code>count, sum, mean, median, mod, max, min, std, var, quantile, skew, kurt, cumprod</code>
   * To statistic by <code>cumsum()</code> such as <code>df["Total sales"] = df["sales"].cumsum()</code>

 ### Data Visualizations by Matplotlib
 Matplotlib is a 2D plotting library for Python that produces publication-quality graphics in a variety of hardcopy formats and in a cross-platform interactive environment. With Matplotlib, developers can generate plots, histograms, power spectra, bar graphs, error graphs, scatter plots, etc. with just a few lines of code.
 
 * Simple Line Chart, for example: <br>
   <code>%matplotlib inline</code><br>
   <code>import matplotlib.pyplot as plt</code><br>
   <code>plt.style.use('seaborn-whitegrid')</code><br>
   <code>import numpy as np</code><br>
   <code>fig = plt.figure()</code><br>
   <code>ax = plt.axes()</code><br>
   <code>x = np.linspace(0, 10, 1000)</code><br>
   <code>ax.plot(x, np.sin(x)); or plt.plot(x, np.sin(x)); or plt.plot(x, np.cos(x));</code>
 * Adjusting Line Charts: Line Color and Style<br>
   <code>plt.plot(x, np.sin(x - 0), color='blue')</code><br>
   <code>plt.plot(x, np.sin(x - 1), color='g')</code><br>    # rgbcmyk</code><br>
   <code>plt.plot(x, np.sin(x - 2), color='0.75')        # 0-1</code><br>
   <code>plt.plot(x, np.sin(x - 3), color='#FFDD44')     # 16Hex RRGGBB</code><br>
   <code>plt.plot(x, np.sin(x - 4), color=(1.0,0.2,0.3)) # RGB 0-1</code><br>
   <code>plt.plot(x, np.sin(x - 5), color='chartreuse');</code><br>
  ...<br>
  <code>plt.plot(x, x + 0, linestyle='solid')</code><br>
  <code>plt.plot(x, x + 1, linestyle='dashed')</code><br>
  <code>plt.plot(x, x + 2, linestyle='dashdot')</code><br>
  <code>plt.plot(x, x + 3, linestyle='dotted');</code><br>
  ...<br>
  <code>plt.plot(x, x + 4, linestyle='-')  </code><br>
  <code>plt.plot(x, x + 5, linestyle='--') </code><br>
  <code>plt.plot(x, x + 6, linestyle='-.') </code><br>
  <code>plt.plot(x, x + 7, linestyle=':');</code><br>
* 
