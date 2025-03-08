
#### Intro
Data cleaning is the process of modifying given data for further processes.
- `inplace = True`
  its used to edit or modify the data directly from the data frame without making a copy.
- `pd.NA` &  `np.nan`
  both are used to rep not available data but `pd.NA` stores an integer value while `np.nan` stores a floating point value. 

---
#### Dropping rows with NAN values

```
# Replacing a row with anny column with a NAN value
df.dropna(inplace = True)

# Reaplacing a row if a specific coumn has a NAN value
df.dropna(select["col1","col2","coln"], inplace = True)
```

#### Replacing NAN values with mean

```
# Replacing all NAN values with the column mean
df.fillna(df.mean(), inplace = True)

# Replacing all NAN values in specific columns with their column means
df.fillna(df.select_dtypes(include=['number']).mean(), inplace = True)
df[["col1", "col2"]] = df[["col1", "col2"]].fillna(df[["col1", "col2"]].mean())

# Replacing all NAN values in specific columns with their column medians
df.fillna(df.select_dtypes(include=['number']).mean(), inplace = True)
df[["col1", "col2"]] = df[["col1", "col2"]].fillna(df[["col1", "col2"]].median())

# Here no inplace =True is used bc we are assigning the modified value back to the originnal data frame instead of modifying the dataframe directly

```

---
#### Handling duplicate data

```
# Removing duplicate rows from the dataframe
df.drop_duplicates(inplace = True)

# Reoving rows with specific duplicate column values
df.drop_duplicates(subset=["col1","col2"], inplace = True)
```

---
#### Handling outliers

- outliers are extreme values
- they might tend to distort or give a false overview of the data and graphs bc of very few extreme values.
- Interquartile range(IQR) is used here for handling outliers.
###### IQR (Interquartile Range)

- The Interquartile Range (IQR) is a method used to detect outliers by measuring the spread of the middle 50% of your data.

1. Q1 (First Quartile / 25th Percentile):
    - This is the value at which 25% of the data points are below it.
    - It represents the lower boundary of the middle 50% of the data.
2. Q3 (Third Quartile / 75th Percentile):
    - This is the value at which 75% of the data points are below it.
    - It represents the upper boundary of the middle 50% of the data.
3. IQR (Interquartile Range) Calculation:
    IQR=Q3−Q1IQR = Q3 - Q1IQR=Q3−Q1
    - This gives us the range between Q1 and Q3, where the middle 50% of values lie.
  
```
Q1 = df[col].quantile(0.25)
Q3 = df[col].quantile(0.75)
IQR = Q3 - Q1

l_limit = Q1 - 1.5 * IQR
u_limit = Q3 + 1.5 * IQR

df = df[(df["col"] >= l_limit) & (df[col] <= u_limit)]
```

- outliers also can be replaced with mean or median.
- chose mean or median according to  the  data.
```
median = df["col"].median()  # or use mean()
df.loc[(df["col"] < lower_bound) | (df["col"] > upper_bound), "col"] = median
```

- for skewed datas, log transformation is used.
```
df["col"] = np.log1p(df["col"])  # log(1 + x) to handle zero values
```

---
#### Replacing incorrect data

1. Finding unique values
	`df["col"].unique()`
	- Helps spot weird values like typos (`"Male", "male", "M", "m"`).
2. Replacing Incorrect Values
```
# Here "M", "m", "Male" is replaced by "male".
df["col"].replace({"M": "male", "m": "male", "Male": "male" }, inplace = True) 
```
3. Filtering invalid entries
```
# Removes negative age values
df = df[df["age"] > 0]
```

---
#### Scaling of data (Standardising and Normalising)
###### Standardization (Z-score)
- For models like linear regression, SVM & KNN Z-Score method is used to standardize.
- $$Z=\frac{X_i-\mu}{n}$$
- Z-score =  (X_i - mean)/ standard deviation
- Z-Scores of a column has mean=0 & standard deviation=1.
- can be used when data has different units (e.g., height in cm vs weight in kg). 
- keeps a fixed range (0 to 1).
- 
```
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
df[["col1", "col2"]] = scaler.fit_transform(df[["col1", "col2"]])
```
###### Normalization (MInMaxScaling)
- scales values between 0 & 1.
- Useful when models need a fixed range, like Neural Networks and KNN.
- Doesn’t change the shape of the data, just rescales it.
- $$X_{normalized}=\frac{X_i-X_{min}}{X_{max}-X_{min}}$$
```
from sklearn.preprocessing import MinMaxScaler
scaler = MinMaxScaler()
df[["col1", "col2"]] = scaler.fit_transform(df[["col1", "col2"]])
```

---
