---
title: "Python #4: Pandas"
date: 2019-02-01
tags: []
excerpt: "Series, DataFrames, Methods, & Functions"
---

This is the fourth in a series of Python notes I made during the [Kubrick](https://kubrickgroup.com/) Data Engineering training course.    
[#1: Basics]({{ site.url }}{{ site.baseurl }}/python-basics)  
[#2: Advanced]({{ site.url }}{{ site.baseurl }}/python-advanced)  
[#3: Scraping]({{ site.url }}{{ site.baseurl }}/python-scraping)  
[#4: Pandas]({{ site.url }}{{ site.baseurl }}/python-pandas)  
[#5: Matplotlib]({{ site.url }}{{ site.baseurl }}/python-matplotlib)  

---
[Pandas](https://pandas.pydata.org/) is an open source Python library for data handling and analysis derived from [numpy](http://www.numpy.org/). It allows for flexible table manipulation among many other features.  

---
# Series
Pandas series are 1-dimensional arrays of indexed data of a single data type.  

```python
import pandas as pd

s = pd.Series([1,2,3], index = ['a','b','c']) # Construct series explicitly
s = pd.Series({'a':1, 'b':2, 'c':3}) # Construct series from dictionary

type(s.values)
# numpy.ndarray
```  

### Indexes
A Pandas index in an object itself and can be thought of as a generated immutable array, they can be created on their own or as part of a Series or DataFrame.  

```python
idx = pd.Index([1,2,3])
print(idx)
# Int64Index([1, 2, 3], dtype='int64')
```  

Pandas also includes **DatetimeIndex**:  

```python
from datetime import datetime
pd.Index([datetime(2019,1,1), datetime(2019,1,2)])
# DatetimeIndex(['2019-01-01', '2019-01-02'], dtype='datetime64[ns]', freq=None)

```

---
# DataFrames
DataFrames are 2-dimensional arrays indexed by columns and rows where each column references a Series object. A DataFrame can be defined relatively easily from many other types of tabular data objects.  

|Data source|Format|Code|
|---|---|---|
|Series|`s = pd.Series([[1,2],[3,4]])`|`df = pd.DataFrame(s)`|
|List of lists|`s = [[1, 2],[3, 4]]`|`df = pd.DataFrame(s, columns=['a', 'b'])`|
|List of dictionaries|`s = [{'a':1, 'b':2}, {'a':3, 'b':4}]`|`df = pd.DataFrame(s)`|
|Dictionary of lists|`s = {'a':[1,3], 'b':[2,4]}`|`df = pd.DatFrame(s)`|
|Dictionary of series|`s1 = pd.Series([1,3]) s2 = pd.Series([2,4])`|`pd.DataFrame({'a':s1, 'b':s2})`|  

Basic information about a DataFrame or Series can be returned using `df.index`, `df.columns`, `df.values`, & `df.shape`.  

---
# Indexing

### .loc
loc is the **explicit** indexing method, it references the actual index value ('row1'/'2019-01-01' etc.). Either use `s.loc[1]` for Series or `df.loc[1, 'col1']` for DataFrame.    

### .iloc
iloc is the **implicit** indexing method, it references the index order value (0, 1, 2, 3 ...). Either use `s.iloc[0]` for Series or `df.iloc[0, 0]` for DataFrame.  

### Slicing
loc and iloc can be used with slices, the output will depend on which is used.  

```python
s = pd.Series(['a', 'b', 'c', 'd'], index=[1,2,3,4])

print(s.loc[1:3])   # When explicit indexing, value at 'stop' is included
# 1  a
# 2  b
# 3  c

print(s.iloc[1:3])   # When implicit indexing, value at 'stop' is NOT included (normal python)
# 2  b
# 3  c
```

DataFrame columns can also be selected using a list of column names using `df[colslist]`.  

### Boolean masks
Boolean masks are truth masks used to filter data, they can be defined explicitly or derived from an operator expression  

```python
mask = [True, True, False, False, True] # Explicit definition
mask = (s>=3) # Operator definition
mask = s.isin([1,4]) # Mask using .isin with a list

s[mask] # Apply to series or DataFrame
```

---
# Manipulating data

### Missing Data

```python
df.dropna(axis=0) # Drop all rows with nan
df['c1'].isnull() # Returns boolean series showing which locations are null
df.fillna(999) # Fill all null values with 999
df.fillna(method='ffill')  # Replaces nulls with previous value in column (also 'bfill')
df.loc[df['c1'].isnull(), 'c1'] = 999 # For all rows where c1 is null, change null to 999
```

### Combining DataFrames
DataFrames can be combined using either `.concat`, `.append`, or `.merge`.  

```python
pd.concat([df1, df2], axis=0, sort=True) # Concat rows (UNION)
pd.concat([df1, df2], axis=1, sort=True) # Concat columns (JOIN)

df1.append(df2, sort=True) # Append rows (same output as concat rows)

pd.merge(df1, df2, on='a', how='inner') # Inner join on column 'a'
pd.merge(df1, df2, left_on='a', right_on='c', how='inner', suffixes=['_left', '_right']) # Inner join on df1['a'] = df2['c']
```

### Sorting
DataFrames can be sorted by index or by column values.  

```python
df.sort_index()  # Index sort
df.sort_values(['a', 'b'], ascending=[True, False])  # Sort on col a ascending then col b descending
```

---
# Operations

### Universal Functions
Pandas uses numpy [universal functions](https://docs.scipy.org/doc/numpy/reference/ufuncs.html) which offer *index-aligned*, fast element wise operations for Pandas objects.  

```python
# Operate on each value in a column:
df['col1'] + 3  

# Operate on each value in a DataFrame:
df + 3

# .apply applies a function to each value referenced, it's much slower than ufuncs:
df['col1'].apply(add3func)  

# If using on a DataFrame, the axis must be specified:
df.apply(add3func, axis=1)
# axis=1 -> cols  (apply row wise)
# axis=0 -> rows  (apply col wise)

# Perform series wise operation (indexes will be aligned before operation):
df['col1'] * df['col2']

# Perform DataFrame wise operation:
df + 2*df

# Perform column wise operation on DataFrame:
df + [1,2]

# DataFrame aggregation
df.sum(axis=0)  # Sum each column 
```

### Aggregation Functions
Pandas includes all the standard aggregation functions `.mean()`, `.median()`, `quantile(q)`, `.sum()`. When using these on a DataFrame the axis must be specified.  
Additional Pandas aggregation functions incluce `.describe()` which gives all the key aggregate stats from a DataFrame.  
`.T` returns the transpose of the DataFrame.  

### Grouping
Grouping allows for more specific aggregation statements on particular keys and columns. The main syntax is `.groupby(<groupcol>)` which will group all rows with the same `<groupcol>` value, `<groupcol>` will then be set as the new index unless `.reset_index()` is added to the end.  
 
```python
df.groupby('key').sum() # Group by 'key' column and sum other columns for those groups
df.groupby('key')['value1'].sum() # Sum groups only for 'value1' column
df.groupby('key').agg(['sum', 'mean']) # Creates columns for each aggregation under each existing column
df.groupby('key').agg({'value1':np.sum, 'value2':np.min}) # Apply sum to value1 col and min to value2 column
df.groupby('key').filter(lambda x: max(x['value1']) > 5)  # Gives the whole group if that group contains a max value > 5
pd.concat([df, df.groupby('key').transform('mean')], axis=1) # Displays the mean for the group in all group values (like an SQL window function)
```

Pandas also supports **pivot tables** which work like `.groupby` but on 2 or more dimensions. 

```python
# Group by gender and is child, aggregate on height
df.pivot_table(index='gender', columns='is_child', values='height', aggfunc=np.mean)

# Equivalent using groupby
df.groupby(['gender', 'is_child']).mean()
```

### String Methods
As well as the standard `.len()`, `.upper()`, & `.lower()` Pandas includes `.startswith()` & `.endswith()` that return boolean masks, and `.split()` which splits a series into distinct list objects.  

### Time Series Methods
Date and time data can be altered using the datetime module or with Pandas DateTime methods. 

```python
### datetime date objects ###
import datetime as dt

dt.date(2019,3,7)
# datetime.date(2019, 3, 7)

dt.timedelta(days=5)
# datetime.timedelta(5)

### Pandas datetime objects ###
pd.to_datetime('2019-3-7')
# Timestamp('2019-03-07 00:00:00')

pd.Timestamp(2019,3,7)
# Timestamp('2019-03-07 00:00:00')

pd.Timestamp(2019,3,7) + pd.offsets.MonthEnd()
# Timestamp('2019-03-31 00:00:00')

df.resample('M', convention='end').mean() # Downsample data to monthly showing means
df.resample('D').mean() # Upsample to daily (then .ffill or .interpolate to fill values)

```