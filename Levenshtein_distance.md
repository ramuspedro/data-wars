# [Matching Strings by Similarity using Levenshtein distance](https://www.datawars.io/data-science-project/1a9c84e4-matching-strings-by-similarity-using-levenshtein-distance)

1. Create the df dataframe containing the product of the two CSVs

```python
df = pd.DataFrame(
    itertools.product(df1.values[:,0], df2.values[:,0]),
    columns=["CSV 1", "CSV 2"]
)
df.head()
```

2. Create a new column Ratio Score that contains the distance for all the rows in df
```python
df['Ratio Score'] = df.apply(lambda x: fuzz.partial_ratio(x["CSV 1"], x["CSV 2"]), axis=1) 
df.head()
```

3. How many rows have a Ratio score of 90 or more?
```python
df[df['Ratio Score'] >= 90].shape
```

4. What's the corresponding company in CSV2 to AECOM in CSV1?
```python
df_aecom = df[df['CSV 1'] == "AECOM"]
df_aecom[df_aecom['Ratio Score'] >= 90]["CSV 2"]
```

5. What's the corresponding CSV 2 company of Starbucks?
```python
df_star = df[df['CSV 1'] == "Starbucks"]
df_star[df_star['Ratio Score'] >= 90]["CSV 2"]
```

6. Is there a matching company for Pinnacle West Capital Corporation in column CSV 2?
```python
df_match = df[df['CSV 1'] == "Pinnacle West Capital Corporation"]
df_match[df_match['Ratio Score'] >= 90]["CSV 2"]
```

7. How many matching companies are there for County of Los Angeles Deferred Compensation Program?
```python
df_match = df[df['CSV 1'] == "County of Los Angeles Deferred Compensation Program"]
df_match[df_match['Ratio Score'] >= 90].shape
```

8. Is there a matching company for The Queens Health Systems?
```python
```