# ⚽ SQL Analysis: Manchester United's 2014/2015 Season Losses


## 📖 Overview

This repository showcases a SQL query designed to analyze European football match data from DataCamp. It focuses on identifying and ranking Manchester United's losses during the **2014/2015 season** based on goal difference.

This project serves as a practical example of using SQL for sports analytics, demonstrating skills in data retrieval, transformation, and ranking.

---

## 🎯 Project Goal

The primary objective of this query is to:

1.  **Isolate** all matches lost by Manchester United in the 2014/2015 season.
2.  **Rank** these losses based on the absolute goal difference (from largest to smallest), effectively identifying their most significant defeats of that season.

---

## 🛠️ Key SQL Techniques Demonstrated

* **Common Table Expressions (CTEs):** Structured the query using `WITH` for clarity and modularity (`home`, `away`).
* **Conditional Logic:** Implemented `CASE WHEN` statements to determine match outcomes relative to the team of interest.
* **Joins:** Combined data from `match` and `team` tables using `LEFT JOIN`.
* **Window Functions:** Applied `RANK() OVER()` to rank matches based on calculated goal difference without collapsing rows.
* **Filtering:** Used `WHERE` clauses to specify the season and the precise loss conditions for Manchester United.

---

## Tables named `match` and `team` with the relevant columns:
    * `match`: `id`, `country_id`, `season`, 'stage', `date`, `hometeam_id`, `awayteam_id`, `home_goal`, `away_goal`
    * `team`: `team_api_id`, `team_long_name`, `team_short_name`


## ⚙️ How It Works

* **`home` CTE:** Temporarily tags each match with an outcome ('MU Win', 'MU Loss', 'Tie') assuming Manchester United is the home team.
* **`away` CTE:** Does the same, but assumes Manchester United is the away team (inverting the win/loss logic).
* **Main `SELECT`:** Pulls distinct match details (date, teams, score).
* **`RANK()`:** Calculates the `match_rank` based on the largest goal difference (`ABS(home_goal - away_goal)`). The biggest defeat gets rank 1.
* **`JOIN`s:** Connects the `match` table with the `home` and `away` CTEs using the unique match `id`.
* **`WHERE` Clause:** Filters results to the '2014/2015' season and selects only rows where the CTE logic identified a 'MU Loss' for Manchester United, whether they played home or away.
