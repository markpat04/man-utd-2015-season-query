Football Match SQL Analysis

Overview

This repository contains an example SQL query used for data analysis on a European football (soccer) database. It demonstrates the use of Common Table Expressions (CTEs), JOINs, CASE statements, and window functions (RANK()) to derive specific insights from raw match data.

This project serves as a portfolio piece demonstrating SQL skills for data analysis.

Project Goal

The goal of the main query is to analyze the performance of a specific team, Manchester United, during the 2014/2015 season. Specifically, it aims to:

Identify all matches that Manchester United lost during that season.

Rank these losses by the largest goal difference, pinpointing their "biggest" defeats of the season.

The SQL Query

Here is the query used to find and rank Manchester United's losses in the 2014/2015 season.

-- Set up the home team CTE
WITH home AS (
  SELECT m.id, t.team_long_name,
    CASE WHEN m.home_goal > m.away_goal THEN 'MU Win'
         WHEN m.home_goal < m.away_goal THEN 'MU Loss' 
         ELSE 'Tie' END AS outcome
  FROM match AS m
  LEFT JOIN team AS t ON m.hometeam_id = t.team_api_id),
-- Set up the away team CTE
away AS (
  SELECT m.id, t.team_long_name,
    CASE WHEN m.home_goal > m.away_goal THEN 'MU Loss'
         WHEN m.home_goal < m.away_goal THEN 'MU Win' 
         ELSE 'Tie' END AS outcome
  FROM match AS m
  LEFT JOIN team AS t ON m.awayteam_id = t.team_api_id)
-- Select columns and and rank the matches by goal difference
SELECT DISTINCT
    m.date,
    home.team_long_name AS home_team,
    away.team_long_name AS away_team,
    m.home_goal, m.away_goal,
    RANK() OVER (ORDER BY ABS(m.home_goal - m.away_goal) DESC) as match_rank
-- Join the CTEs onto the match table
FROM match AS m
LEFT JOIN home ON m.id = home.id
LEFT JOIN away ON m.id = away.id
WHERE m.season = '2014/2015'
      AND ((home.team_long_name = 'Manchester United' AND home.outcome = 'MU Loss')
      OR (away.team_long_name = 'Manchester United' AND away.outcome = 'MU Loss'));


Query Breakdown

WITH home AS (...): This CTE creates a temporary table that identifies all matches and assigns an outcome ('Win', 'Loss', 'Tie') from the perspective of the home team.

WITH away AS (...): This CTE does the same, but assigns the outcome from the perspective of the away team. Notice the win/loss logic is inverted from the home CTE.

SELECT DISTINCT ...: The main query selects the match details.

RANK() OVER (...): This window function calculates a rank for each match. It orders the results by the absolute difference between home and away goals in descending order (so the largest goal difference gets rank 1).

FROM match AS m ...: The query joins the main match table with the two CTEs using the match id.

WHERE m.season = ...: This filters the data to only include the '2014/2015' season.

WHERE ... (home.team_long_name ...): This is the key logic. It finds all matches from that season where either the home team was 'Manchester United' and the outcome was a 'Loss', or the away team was 'Manchester United' and the outcome was a 'Loss'.

How to Use

This query is intended to be run on a SQL database (like PostgreSQL, MySQL, or SQLite) that contains a schema similar to the European Soccer Database, which is a common source for this type of project.

You will need at least two tables:

match (with columns like id, hometeam_id, awayteam_id, home_goal, away_goal, season)

team (with columns like team_api_id, team_long_name)
