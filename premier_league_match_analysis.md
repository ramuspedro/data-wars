# [Premier League Match Analysis](https://www.datawars.io/data-science-project/b0dc1cc3-premier-league-match-analysis)

1. Replace invalid values from the season column
```python
df.loc[df["season"]=="?","season"] = "Unknown season"
df["season"].value_counts()
```

2. Identify invalid values in goals scored
```python
display(df.loc[(df["home_goals"] < 0),["home_goals"]].count())
display(df.loc[(df["away_goals"] < 0),["away_goals"]].count())
```

3. Replace invalid goals for 0
```python
df.loc[(df["home_goals"] < 0),["home_goals"]] = 0
df.loc[(df["away_goals"] < 0),["away_goals"]] = 0
```

4. Identify and clean invalid results in the result column
```python
col         = 'result'
conditions  = [ df["home_goals"] > df["away_goals"], df["home_goals"] < df["away_goals"], df["home_goals"] == df["away_goals"] ]
choices     = [ "H", 'A', 'D' ]
df[col] = np.select(conditions, choices, default="?")
```

5. What's the average number of goals per match?
```python
'{0:.2f}'.format((df["home_goals"].sum() + df["away_goals"].sum()) / (df["home_goals"].count()))
```

6. Create a new column total_goals
```python
df["total_goals"] = df["home_goals"] + df["away_goals"]
df["total_goals"]
```

7. Calculate average goals per season
```python
goals_per_season = df.groupby(by="season")["total_goals"].mean()
goals_per_season
```

8. What's the biggest goal difference in a match?
```python
np.abs(df["home_goals"] - df["away_goals"]).max()
```

9. What's the team with most away wins?
```python
df.loc[df["result"] == "A"].groupby("away_team")["away_team"].value_counts().sort_values(ascending=False)
```

10. What's the team with the most goals scored at home?
```python
df.groupby("home_team")["home_goals"].sum().sort_values(ascending=False)
```

11. What's the team that received the least amount of goals while playing at home?
```python
(df.groupby("home_team")["away_goals"].sum()/df.groupby("home_team")["away_goals"].count()).sort_values()
```

12. What's the team with most goals scored playing as a visitor (away from home)?
```python
df.groupby("away_team")["away_goals"].sum().sort_values(ascending=False)
```