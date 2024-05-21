# [Capstone Project: Cleaning Google Playstore data](https://www.datawars.io/data-science-project/43c0d204-capstone-project-cleaning-google-playstore-data)

```python
import pandas as pd
import numpy as np
import seaborn as sns
import missingno as msno

df = pd.read_csv('googleplaystore.csv')
df.sample(5)
```

1. Which of the following column(s) has/have null values?
```python
df.columns[df.isna().sum() > 0]
```

2. Clean the Rating column and the other columns containing null values
```python
df.loc[df["Rating"] > 5, "Rating"] = np.nan
df.loc[df["Rating"].isna(), "Rating"] = df.loc[~df["Rating"].isna(), "Rating"].mean()
df.dropna(inplace=True)
```

3. Clean the column Reviews and make it numeric (difícil)
```python
df['Reviews'] = df['Reviews'].replace({'M': 'e+06', 'K': 'e+03'}, regex=True).astype(float)
df.info()
```

4. How many duplicated apps are there? (difícil)
```python
df2 = pd.DataFrame(df[df["App"].duplicated()]["App"].value_counts())
df2["count"] = df2["count"]+1
df2["count"].sum()
```

5. Drop duplicated apps keeping only the ones with the greatest number of reviews
```python
# Your dataframe doesn't match what's expected. Different number of rows. Expected is 9648. Yours has 10829.
# df.sort_values(["Reviews"]).drop_duplicates(['App'], keep='last', inplace=True)

df_sorted = df.sort_values(by=['App', 'Reviews'])

df_sorted.loc[
    df_sorted['App'].duplicated(keep=False) & ~df_sorted.duplicated(keep=False),
    ['App', 'Reviews']
].head(5)

df_sorted.drop_duplicates(subset=['App'], keep='last', inplace=True)
df = df_sorted
```

6. Format the Category column
```python
df_cat = df["Category"].str.split("_")
df_cat = df_cat.str.join(" ")
df_cat = df_cat.str.capitalize()
df["Category"] = df_cat
df["Category"]
```

7. Clean and convert the Installs column to numeric type
```python
df_inst = df["Installs"].str.replace("+", "")
df_inst = df_inst.str.replace(",", "")
df_inst = df_inst.astype("int")
df["Installs"] = df_inst
df.info()
```

8. Clean and convert the Size column to numeric (representing bytes)
```python
df_size = df["Size"].replace({'M': '*1024*1024', 'k': '*1024'}, regex=True)
df_size = df_size.map(lambda x: eval(x) if x.split("*")[-1] == "1024" else 0)
df["Size"] = df_size
```

9. Clean and convert the Price column to numeric
```python
df_price = df["Price"].str.replace("$", "").replace("Free", "0")
df_price = df_price.astype("float")
df["Price"] = df_price
```

10. Paid or free?
```python
df["Distribution"] = df["Price"].map(lambda x: "Paid" if x > 0 else "Free")
df["Distribution"]
```

11. Which app has the most reviews?
```python
df.sort_values(by="Reviews", ascending=False)
```

12. What category has the highest number of apps uploaded to the store?
```python
df.groupby(by="Category")["Category"].agg(["count"]).sort_values("count", ascending=False)
```

13. To which category belongs the most expensive app?
```python
df.sort_values(by="Price", ascending=False)
```

14. What's the name of the most expensive game?
```python
df[df["Category"]=="Game"].sort_values(by=["Price"], ascending=False)
```

15. Which is the most popular Finance App?
```python
df[(df["Category"]=="Game") & (df["Content Rating"]=="Teen")].sort_values(by="Reviews", ascending=False)
```

16. What Teen Game has the most reviews?
```python
df[(df["Category"]=="Game") & (df["Content Rating"]=="Teen")].sort_values(by="Reviews", ascending=False)
```

17. Which is the free game with the most reviews?
```python
df[(df["Distribution"]=="Free") & (df["Category"]=="Game")].sort_values(by=["Reviews"], ascending=False)
```

18. How many Tb (tebibytes) were transferred (overall) for the most popular Lifestyle app?
```python
df_calc = df[df["Category"]=="Lifestyle"].sort_values(by="Reviews", ascending=False).head(1)
df_calc["Size"]*df_calc["Installs"] / (1024*1024*1024*1024)
```

