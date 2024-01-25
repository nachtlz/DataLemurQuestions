# Data Lemur Interview Questions

The questions in this file are taken from the following link: [Visit DataLemur Questions](https://datalemur.com/questions) 

# 1. Histogram of Tweets [Twitter SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/twitter.svg" width="20" height="20">

Assume you're given a table Twitter tweet data, write a query to obtain a histogram of tweets posted per user in 2022. Output the tweet count per user as the bucket and the number of Twitter users who fall into that bucket.

In other words, group the users by the number of tweets they posted in 2022 and count the number of users in each group.

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

### Example Output: Tweet Histogram

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

# 2. Data Science Skills [LinkedIn SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/linkedin.svg" width="20" height="20">

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

# 3. Page With No Likes [Facebook SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/facebook.svg" width="20" height="20">

Assume you're given two tables containing data about Facebook Pages and their respective likes (as in "Like a Facebook Page").

Write a query to return the IDs of the Facebook pages that have zero likes. The output should be sorted in ascending order based on the page IDs.

## `pages` Table:

| Column Name | Type    |
|-------------|---------|
| page_id     | integer |
| page_name   | varchar |

### Example Input:

| page_id | page_name             |
|---------|-----------------------|
| 20001   | SQL Solutions         |
| 20045   | Brain Exercises       |
| 20701   | Tips for Data Analysts |

## `page_likes` Table:

| Column Name | Type      |
|-------------|-----------|
| user_id     | integer   |
| page_id     | integer   |
| liked_date  | datetime  |

### Example Input:

| user_id | page_id | liked_date           |
|---------|---------|----------------------|
| 111     | 20001   | 04/08/2022 00:00:00 |
| 121     | 20045   | 03/12/2022 00:00:00 |
| 156     | 20001   | 07/25/2022 00:00:00 |

### Example Output:

| page_id |
|---------|
| 20701   |

### Query:

```sql
SELECT
    p.page_id
FROM
    pages p
LEFT JOIN
    page_likes pl ON p.page_id = pl.page_id
WHERE
    pl.page_id IS NULL
ORDER BY
    p.page_id;
```

# 4. Unfinished Parts [Tesla SQL Interview Question]

Tesla is currently investigating production bottlenecks and needs your assistance to extract relevant data. The task is to write a query that determines which parts have begun the assembly process but are not yet finished.

## Assumptions:

- The `parts_assembly` table contains all parts currently in production, each at varying stages of the assembly process.
- An unfinished part is one that lacks a `finish_date`.

This question is straightforward, so let's approach it with simplicity in both thinking and solution.

## `parts_assembly` Table:

| Column Name   | Type      |
|---------------|-----------|
| part          | string    |
| finish_date   | datetime  |
| assembly_step | integer   |

### Example Input:

| part    | finish_date           | assembly_step |
|---------|-----------------------|----------------|
| battery | 01/22/2022 00:00:00   | 1              |
| battery | 02/22/2022 00:00:00   | 2              |
| battery | 03/22/2022 00:00:00   | 3              |
| bumper  | 01/22/2022 00:00:00   | 1              |
| bumper  | 02/22/2022 00:00:00   | 2              |
| bumper  |                       | 3              |
| bumper  |                       | 4              |

### Example Output:

| part   | assembly_step |
|--------|---------------|
| bumper | 3             |
| bumper | 4             |

### Query:

```sql
SELECT
    pa.part,
    pa.assembly_step
FROM
    parts_assembly pa
WHERE
    pa.finish_date IS NULL;
```

# 5. Laptop vs. Mobile Viewership [New York Times SQL Interview Question]

Assume you're given a table on user viewership categorized by device type, where the three types are laptop, tablet, and phone.

Write a query that calculates the total viewership for laptops and mobile devices, where mobile is defined as the sum of tablet and phone viewership. Output the total viewership for laptops as `laptop_views` and the total viewership for mobile devices as `mobile_views`.

## `viewership` Table:

| Column Name | Type      |
|-------------|-----------|
| user_id     | integer   |
| device_type | string    | ('laptop', 'tablet', 'phone')
| view_time   | timestamp |

### Example Input:

| user_id | device_type | view_time           |
|---------|--------------|---------------------|
| 123     | tablet       | 01/02/2022 00:00:00 |
| 125     | laptop       | 01/07/2022 00:00:00 |
| 128     | laptop       | 02/09/2022 00:00:00 |
| 129     | phone        | 02/09/2022 00:00:00 |
| 145     | tablet       | 02/24/2022 00:00:00 |

### Example Output:

| laptop_views | mobile_views |
|--------------|--------------|
| 2            | 3            |

### Query 1:

```sql
WITH laptop_views AS (
    SELECT COUNT(*) AS laptop_views
    FROM viewership
    WHERE device_type = 'laptop'
), mobile_views AS (
    SELECT COUNT(*) AS mobile_views
    FROM viewership
    WHERE device_type != 'laptop'
)
SELECT
    laptop_views,
    mobile_views
FROM
    laptop_views, mobile_views;
```

### Query 2:

```sql
SELECT 
  SUM(CASE WHEN device_type = 'laptop' THEN 1 ELSE 0 END) AS laptop_views, 
  SUM(CASE WHEN device_type IN ('tablet', 'phone') THEN 1 ELSE 0 END) AS mobile_views 
FROM viewership;
```

# 6. Average Post Hiatus (Part 1) [Facebook SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/facebook.svg" width="20" height="20">

Given a table of Facebook posts, the task is to, for each user who posted at least twice in 2021, write a query to find the number of days between each user’s first post of the year and last post of the year in 2021. Output the user and the number of days between each user's first and last post.

## `posts` Table:

| Column Name | Type      |
|-------------|-----------|
| user_id     | integer   |
| post_id     | integer   |
| post_date   | timestamp |
| post_content| text      |

### Example Input:

| user_id | post_id | post_date           | post_content                                               |
|---------|---------|---------------------|------------------------------------------------------------|
| 151652  | 599415  | 07/10/2021 12:00:00 | Need a hug                                                 |
| 661093  | 624356  | 07/29/2021 13:00:00 | Bed. Class 8-12. Work 12-3. Gym 3-5 or 6. Then class 6-10..|
| 004239  | 784254  | 07/04/2021 11:00:00 | Happy 4th of July!                                         |
| 661093  | 442560  | 07/08/2021 14:00:00 | Just going to cry myself to sleep after watching Marley ...|
| 151652  | 111766  | 07/12/2021 19:00:00 | I'm so done with covid - need travelling ASAP!             |

### Example Output:

| user_id | days_between |
|---------|--------------|
| 151652  | 2            |
| 661093  | 21           |

### Query:

```sql
SELECT
    user_id,
    DATE_PART('day', MAX(post_date) - MIN(post_date)) AS days_between
FROM
    posts
WHERE
    DATE_PART('year', post_date) = 2021
GROUP BY
    user_id
HAVING
    COUNT(user_id) > 1;
```

# 7. Teams Power Users [Microsoft SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/microsoft.svg" width="20" height="20">

Write a query to identify the top 2 Power Users who sent the highest number of messages on Microsoft Teams in August 2022. Display the IDs of these 2 users along with the total number of messages they sent. Output the results in descending order based on the count of the messages.

## Assumption:

No two users have sent the same number of messages in August 2022.

## `messages` Table:

| Column Name | Type      |
|-------------|-----------|
| message_id  | integer   |
| sender_id   | integer   |
| receiver_id | integer   |
| content     | varchar   |
| sent_date   | datetime  |

### Example Input:

| message_id | sender_id | receiver_id | content                   | sent_date           |
|------------|-----------|-------------|---------------------------|---------------------|
| 901        | 3601      | 4500        | You up?                   | 08/03/2022 00:00:00 |
| 902        | 4500      | 3601        | Only if you're buying     | 08/03/2022 00:00:00 |
| 743        | 3601      | 8752        | Let's take this offline   | 06/14/2022 00:00:00 |
| 922        | 3601      | 4500        | Get on the call           | 08/10/2022 00:00:00 |

### Example Output:

| sender_id | message_count |
|-----------|---------------|
| 3601      | 2             |
| 4500      | 1             |

### Query:

```sql
SELECT
    sender_id,
    COUNT(*) AS message_count
FROM
    messages
WHERE
    DATE_PART('month', sent_date) = '08' AND DATE_PART('year', sent_date) = '2022'
GROUP BY
    sender_id
ORDER BY
    message_count DESC
LIMIT
    2;
```

# 8. Factorial Formula [Microsoft Python Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/microsoft.svg" width="20" height="20">

Given a number \( n \), write a formula that returns \( n! \), the factorial of \( n \).

In case you forgot the factorial formula:

$n! = n \times (n-1) \times (n-2) \times \ldots \times 2 \times 1 \$

For example, $\( 5! = 5 \times 4 \times 3 \times 2 \times 1 = 120 \)$, so we'd return 120.

Assume \( n \) is a non-negative integer.

### Function:

```python
def factorial(n):
    if n < 1:
        return 1
    else:
        return n * factorial(n - 1)
```

# 9. Fizz Buzz Sum [Google Python Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/google.svg" width="20" height="20">

Write a function `fizz_buzz_sum` to find the sum of all multiples of 3 or 5 below a target value.

For example, if the target value was 10, the multiples of 3 or 5 below 10 are 3, 5, 6, and 9.

Because $\(3 + 5 + 6 + 9 = 23\)$, our function would return 23.

### Function:

```python
def fizz_buzz_sum(target):
  sum = 0
  for i in range(target):
    if (i % 3 == 0 or i % 5 == 0):
      sum += i
  return sum
```

# 10. Intersection of Two Lists [Amazon Python Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/amazon.svg" width="20" height="20">

Write a function to get the intersection of two lists.

For example, if $\(A = [1, 2, 3, 4, 5]\)$, and $\(B = [0, 1, 3, 7]\)$, then you should return $\([1, 3]\)$.

### Iterative function:

```python
def intersection(a, b):
    result = []
    for i in a:
        if i in b:
            result.append(i)
    return result
```

### Using Python Methods:

```python
def intersection(a, b):
    return list(set(a) & set(b))
```
