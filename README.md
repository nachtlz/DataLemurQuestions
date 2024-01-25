# Data Lemur Interview Questions

The questions in this file are taken from the following link: [Visit DataLemur Questions](https://datalemur.com/questions)

## 1. Histogram of Tweets [Twitter SQL Interview Question] [![Twitter Icon](http://i.imgur.com/wWzX9uB.png)]()

Assuming you have a table named `tweets` with the following columns:

| Column Name | Type    |
|-------------|---------|
| tweet_id    | integer |
| user_id     | integer |
| msg         | string  |
| tweet_date  | timestamp |

### Example Input:

| tweet_id | user_id | msg                                               | tweet_date           |
|----------|---------|---------------------------------------------------|----------------------|
| 214252   | 111     | Am considering taking Tesla private at $420...   | 12/30/2021 00:00:00 |
| 739252   | 111     | Despite the constant negative press covfefe      | 01/01/2022 00:00:00 |
| 846402   | 111     | Following @NickSinghTech on Twitter changed...   | 02/14/2022 00:00:00 |
| 241425   | 254     | If the salary is so competitive why won’t...     | 03/01/2022 00:00:00 |
| 231574   | 148     | I no longer have a manager. I can't be managed   | 03/23/2022 00:00:00 |

## Example Output: Tweet Histogram

| tweet_bucket | users_num |
|--------------|-----------|
| 1            | 2         |
| 2            | 1         |

### Query:

```sql
SELECT
    tweet_bucket,
    COUNT(*) AS users_num
FROM (
    SELECT
        COUNT(*) AS tweet_bucket
    FROM
        tweets t
    WHERE
        EXTRACT(YEAR FROM t.tweet_date) = '2022'
    GROUP BY
        t.user_id
    ORDER BY
        COUNT(*)
) AS user_tweet_counts
GROUP BY
    user_tweet_counts.tweet_bucket;
```

## 2. Data Science Skills [LinkedIn SQL Interview Question] [![LinkedIn Icon](http://i.imgur.com/q63QSE5.png)]()

Given a table of candidates and their skills, the task is to identify candidates best suited for an open Data Science job. The goal is to find candidates proficient in Python, Tableau, and PostgreSQL.

Write a query to list the candidates who possess all of the required skills for the job. Sort the output by candidate ID in ascending order.

### Assumption

There are no duplicates in the candidates table.

| Column Name   | Type    |
|---------------|---------|
| candidate_id  | integer |
| skill         | varchar |

### Example Input

| candidate_id  | skill      |
|---------------|------------|
| 123           | Python     |
| 123           | Tableau    |
| 123           | PostgreSQL |
| 234           | R          |
| 234           | PowerBI    |
| 234           | SQL Server |
| 345           | Python     |
| 345           | Tableau    |

### Example Output

| candidate_id  |
|---------------|
| 123           |

### Query:

```sql
SELECT
    candidate_id
FROM
    candidates
WHERE
    skill IN ('Python', 'Tableau', 'PostgreSQL')
GROUP BY
    candidate_id
HAVING
    COUNT(skill) = 3
ORDER BY
    candidate_id;
```
