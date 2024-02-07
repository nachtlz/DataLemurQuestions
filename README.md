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

# 11. Missing Integer [Amazon Python Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/amazon.svg" width="20" height="20">

Given an input list containing $\(n\)$ distinct numbers in the range 0 to $\(n\)$, return the only number in the range that is missing from the list.

For example, given `input = [0, 1, 3]`, return 2. Because the input list has 3 elements, we expect to see the numbers 0 to 3 in there, but 2 is missing.

Another example: given `input = [4, 3, 2, 1]`, return 0. We return 0 because the input list has 4 elements, so we expect to see the numbers 0 to 4 in there, but 0 itself is missing!

### Function:

```python
def missing_int(input: list[int])-> int:
  for i in range(len(input)):
    if i in input:
      continue
    return i
  return len(input)
```

# 12. Duplicate Job Listings [Linkedin SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/linkedin.svg" width="20" height="20">

Assume you're given a table containing job postings from various companies on the LinkedIn platform. Write a query to retrieve the count of companies that have posted duplicate job listings.

## Definition:

Duplicate job listings are defined as two job listings within the same company that share identical titles and descriptions.

## `job_listings` Table:

| Column Name | Type    |
|-------------|---------|
| job_id      | integer |
| company_id  | integer |
| title       | string  |
| description | string  |

### Example Input:

| job_id | company_id | title           | description                                                                                                                  |
|--------|------------|-----------------|------------------------------------------------------------------------------------------------------------------------------|
| 248    | 827        | Business Analyst| Business analyst evaluates past and current business data with the primary goal of improving decision-making processes within organizations. |
| 149    | 845        | Business Analyst| Business analyst evaluates past and current business data with the primary goal of improving decision-making processes within organizations. |
| 945    | 345        | Data Analyst    | Data analyst reviews data to identify key insights into a business's customers and ways the data can be used to solve problems. |
| 164    | 345        | Data Analyst    | Data analyst reviews data to identify key insights into a business's customers and ways the data can be used to solve problems. |
| 172    | 244        | Data Engineer   | Data engineer works in a variety of settings to build systems that collect, manage, and convert raw data into usable information for data scientists and business analysts to interpret. |

### Example Output:

| duplicate_companies |
|---------------------|
| 1                   |

### Query:

```sql
SELECT
    COUNT(*) AS duplicate_companies
FROM (
    SELECT
        COUNT(*) AS duplicate_companies
    FROM
        job_listings
    GROUP BY
        company_id, title, description
    HAVING
        COUNT(DISTINCT job_id) > 1
) AS dc;
```

# 13. Cities With Completed Trades [Robinhood SQL Interview Question]

Assume you're given the tables containing completed trade orders and user details in a Robinhood trading system.

Write a query to retrieve the top three cities that have the highest number of completed trade orders listed in descending order. Output the city name and the corresponding number of completed trade orders.

## `trades` Table:

| Column Name | Type    |
|-------------|---------|
| order_id    | integer |
| user_id     | integer |
| price       | decimal |
| quantity    | integer |
| status      | string  |
| timestamp   | datetime|

### Example Input:

| order_id | user_id | price | quantity | status    | timestamp           |
|----------|---------|-------|----------|-----------|---------------------|
| 100101   | 111     | 9.80  | 10       | Cancelled | 08/17/2022 12:00:00 |
| 100102   | 111     | 10.00 | 10       | Completed | 08/17/2022 12:00:00 |
| 100259   | 148     | 5.10  | 35       | Completed | 08/25/2022 12:00:00 |
| 100264   | 148     | 4.80  | 40       | Completed | 08/26/2022 12:00:00 |
| ...      | ...     | ...   | ...      | ...       | ...                 |

## `users` Table:

| Column Name | Type    |
|-------------|---------|
| user_id     | integer |
| city        | string  |
| email       | string  |
| signup_date | datetime|

### Example Input:

| user_id | city          | email                            | signup_date         |
|---------|---------------|----------------------------------|---------------------|
| 111     | San Francisco | rrok10@gmail.com                | 08/03/2021 12:00:00 |
| 148     | Boston        | sailor9820@gmail.com             | 08/20/2021 12:00:00 |
| 178     | San Francisco | harrypotterfan182@gmail.com     | 01/05/2022 12:00:00 |
| 265     | Denver        | shadower_@hotmail.com            | 02/26/2022 12:00:00 |
| ...     | ...           | ...                              | ...                 |

### Example Output:

| city          | total_orders |
|---------------|--------------|
| San Francisco | 3            |
| Boston        | 2            |
| Denver        | 1            |

### Query:

```sql
SELECT
    u.city,
    COUNT(*) AS total_orders
FROM
    trades t
JOIN
    users u ON t.user_id = u.user_id
WHERE
    t.status = 'Completed'
GROUP BY
    u.city
ORDER BY
    total_orders DESC
LIMIT
    3;
```

# 14. Contains Duplicate [Apple Python Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/apple.svg" width="20" height="20">

Given a list of integers called `input`, return True if any value appears at least twice in the array. Return False if every element in the input list is distinct.

For example, if the input list was `[1, 3, 5, 7, 1]`, then return True because the number 1 shows up twice.

However, if the input was `[1, 3, 5, 7]`, then return False, because every element of the list is distinct.

### Function:

```python
def contains_duplicate(input)-> bool:
  l = set()
  for i in input:
    if i in l:
      return True
    else:
      l.add(i)
  return False
```

# 15. Salary Inequity [Microsoft Python Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/microsoft.svg" width="20" height="20">

Given a list of salaries, we'll define a metric called inequity, which is the difference between the maximum and minimum salary seen in the list:

`inequity = max(input_list) - min(input_list)`

Write a function called `min_inequity` that takes in a list of salaries and a value $\( n \)$, and returns the minimum inequity possible when taking $\( n \)$ salaries from the full salary list.

If that was hard to understand, you're not alone – let's break it down with some examples.

### Example 1:
`salaries = [60000, 80000, 120000, 70000]`

`n = 2`

The minimum inequity is $10,000, because max(60000, 70000) - min(60000, 70000) = 10000.

### Example 2:
`salaries = [60000, 80000, 120000, 70000]`

`n = 3`

The minimum inequity is $20,000, because max(60000, 70000, 80000) - min(60000, 70000, 80000) = 20000.

### Function:

```python
def min_inequity(salaries, n):
	salaries.sort()
	return salaries[n-1] - salaries[0]
```

# 16. Max Profit Trading Stocks [Google Python Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/google.svg" width="20" height="20">

You are given a list of stock prices, where the stock's price on the \(i^{th}\) day is stored as the \(i^{th}\) element of the prices list.

You want to maximize your profit trading the stock, but are only allowed to buy the stock once and sell it once on a future day.

Write a function called `max_profit` which takes in this list of stock prices, and computes the max profit possible. Return 0 if you can't make any profit.

### Example 1:
Input: prices = [9, 1, 3, 6, 4, 8, 3, 5, 5]

Output: 7
Explanation: Buy on day 2 (price = 1) and sell on day 6 (price = 8), profit = 8 - 1 = 7.

Note that buying on day 2 and selling on day 1 is not allowed because you have to buy the stock BEFORE you can sell it (unless you're a time-traveller, in which case just trade bitcoin).

### Example 2:
Input: prices = [6, 4, 3, 3, 2]

Output: 0
Explanation: In this case, no trades should be made since the stock is tanking like a brick. The max profit here is 0.

### Function:

```python
def max_profit(prices):
    buy = prices[0]
    profit = 0
    for i in prices:
        if i - buy > profit:
            profit = i - buy
        if i < buy:
            buy = i
    return profit
```

# 17. Average Review Ratings [Amazon SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/amazon.svg" width="20" height="20">

Given the `reviews` table, write a query to retrieve the average star rating for each product, grouped by month. The output should display the month as a numerical value, product ID, and average star rating rounded to two decimal places. Sort the output first by month and then by product ID.

## `reviews` Table:
| Column Name | Type    |
|-------------|---------|
| review_id   | integer |
| user_id     | integer |
| submit_date | datetime|
| product_id  | integer |
| stars       | integer (1-5) |

### `reviews` Example Input:
| review_id | user_id | submit_date        | product_id | stars |
|-----------|---------|--------------------|------------|-------|
| 6171      | 123     | 06/08/2022 00:00:00| 50001      | 4     |
| 7802      | 265     | 06/10/2022 00:00:00| 69852      | 4     |
| 5293      | 362     | 06/18/2022 00:00:00| 50001      | 3     |
| 6352      | 192     | 07/26/2022 00:00:00| 69852      | 3     |
| 4517      | 981     | 07/05/2022 00:00:00| 69852      | 2     |

### Example Output:
| mth | product | avg_stars |
|-----|---------|-----------|
| 6   | 50001   | 3.50      |
| 6   | 69852   | 4.00      |
| 7   | 69852   | 2.50      |

### Query:

```sql
SELECT
    EXTRACT("month" FROM submit_date) AS mth,
    product_id AS product,
    AVG(stars)::numeric(10,2) AS avg_stars
FROM
    reviews
GROUP BY
    mth, product
ORDER BY
    mth, product;
```

# 18. App Click-through Rate (CTR) [Facebook SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/facebook.svg" width="10" height="10">

Assume you have an events table on Facebook app analytics. Write a query to calculate the click-through rate (CTR) for the app in 2022 and round the results to 2 decimal places.

**Definition and note:**

- Percentage of click-through rate (CTR) = 100.0 * Number of clicks / Number of impressions
- To avoid integer division, multiply the CTR by 100.0, not 100.

## `events` Table:

| Column Name | Type    |
|-------------|---------|
| app_id      | integer |
| event_type  | string  |
| timestamp   | datetime|

### `events` Example Input:

| app_id | event_type | timestamp           |
|--------|------------|---------------------|
| 123    | impression | 07/18/2022 11:36:12|
| 123    | impression | 07/18/2022 11:37:12|
| 123    | click      | 07/18/2022 11:37:42|
| 234    | impression | 07/18/2022 14:15:12|
| 234    | click      | 07/18/2022 14:16:12|

### Example Output:

| app_id | ctr    |
|--------|--------|
| 123    | 50.00  |
| 234    | 100.00 |

### Query:

```sql
SELECT 
    app_id, 
    (100.0 * SUM(CASE WHEN event_type = 'click' THEN 1 ELSE 0 END) / 
    SUM(CASE WHEN event_type = 'impression' THEN 1 ELSE 0 END))::numeric(10,2) AS ctr
FROM 
    events
WHERE 
    EXTRACT(YEAR FROM timestamp) = '2022'
GROUP BY 
    app_id;
```

# 19. Second Day Confirmation [TikTok SQL Interview Question] <img src="https://raw.githubusercontent.com/FortAwesome/Font-Awesome/6.x/svgs/brands/tiktok.svg" width="20" height="20">

Assume you're given tables with information about TikTok user sign-ups and confirmations through email and text. New users on TikTok sign up using their email addresses, and upon sign-up, each user receives a text message confirmation to activate their account.

Write a query to display the user IDs of those who did not confirm their sign-up on the first day, but confirmed on the second day.

**Definition:**

`action_date` refers to the date when users activated their accounts and confirmed their sign-up through text messages.

## `emails` Table:

| Column Name | Type    |
|-------------|---------|
| email_id    | integer |
| user_id     | integer |
| signup_date | datetime|

### `emails` Example Input:

| email_id | user_id | signup_date          |
|----------|---------|----------------------|
| 125      | 7771    | 06/14/2022 00:00:00 |
| 433      | 1052    | 07/09/2022 00:00:00 |

## `texts` Table:

| Column Name  | Type    |
|--------------|---------|
| text_id      | integer |
| email_id     | integer |
| signup_action| string ('Confirmed', 'Not confirmed')|
| action_date  | datetime|

### `texts` Example Input:

| text_id | email_id | signup_action | action_date          |
|---------|----------|---------------|----------------------|
| 6878    | 125      | Confirmed     | 06/14/2022 00:00:00 |
| 6997    | 433      | Not Confirmed | 07/09/2022 00:00:00 |
| 7000    | 433      | Confirmed     | 07/10/2022 00:00:00 |

### Example Output:

| user_id |
|---------|
| 1052    |

### Query:

```sql
SELECT 
  DISTINCT user_id 
FROM 
  emails e
JOIN 
  texts t 
ON 
  e.email_id = t.email_id
WHERE 
  t.signup_action = 'Confirmed'
AND 
  DATE_PART('day', t.action_date::timestamp - e.signup_date::timestamp) = 1;
```

# 20. Cards Issued Difference [JPMorgan Chase SQL Interview Question]

Your team at JPMorgan Chase is preparing to launch a new credit card, and to gain some insights, you're analyzing how many credit cards were issued each month.

Write a query that outputs the name of each credit card and the difference in the number of issued cards between the month with the highest issuance cards and the lowest issuance. Arrange the results based on the largest disparity.

## `monthly_cards_issued` Table:
| Column Name | Type    |
|-------------|---------|
| issue_month | integer |
| issue_year  | integer |
| card_name   | string  |
| issued_amount | integer |

### `monthly_cards_issued` Example Input:
| card_name          | issued_amount | issue_month | issue_year |
|--------------------|---------------|-------------|------------|
| Chase Freedom Flex | 55000         | 1           | 2021       |
| Chase Freedom Flex | 60000         | 2           | 2021       |
| Chase Freedom Flex | 65000         | 3           | 2021       |
| Chase Freedom Flex | 70000         | 4           | 2021       |
| Chase Sapphire Reserve | 170000     | 1           | 2021       |
| Chase Sapphire Reserve | 175000     | 2           | 2021       |
| Chase Sapphire Reserve | 180000     | 3           | 2021       |

### Example Output:
| card_name              | difference |
|------------------------|------------|
| Chase Freedom Flex     | 15000      |
| Chase Sapphire Reserve | 10000      |

### Query:

```sql
SELECT card_name,
       (MAX(issued_amount) - MIN(issued_amount)) AS difference
FROM 
  monthly_cards_issued
GROUP BY 
  card_name
ORDER BY 
  difference
DESC;
```

# 21. Compressed Mean [Alibaba SQL Interview Question]

You're trying to find the mean number of items per order on Alibaba, rounded to 1 decimal place using tables which includes information on the count of items in each order (`item_count table`) and the corresponding number of orders for each item count (`order_occurrences table`).

## `items_per_order` Table:
| Column Name      | Type    |
|------------------|---------|
| item_count       | integer |
| order_occurrences | integer |

### `items_per_order` Example Input:
| item_count | order_occurrences |
|------------|-------------------|
| 1          | 500               |
| 2          | 1000              |
| 3          | 800               |
| 4          | 1000              |

There are a total of 500 orders with one item per order, 1000 orders with two items per order, and 800 orders with three items per order.

### Example Output:

| mean |
|------|
| 2.7  |

### Query:

```sql
SELECT 
  ROUND(
    SUM(item_count::DECIMAL*order_occurrences) / SUM(order_occurrences), 1
  ) AS mean
FROM 
  items_per_order;
```
