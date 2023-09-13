<h1 align="center">Real-world example of SQL data analysis</h1>

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sqlda.jpeg" height="35%" width="50%"/>
</p>

<h2>Overview</h2>
This will be a demonstration on how data is analyzed in the real world from big datasets using commands in SQL. In this example we will observe and compare data regarding apps from the Apple Store and answering relevant questions about this dataset such as:

Do paid apps have a better ratings than free apps? 

How many languages do the best-rated apps support?

Which genres of apps are rated low?

What do the best apps have: longer descriptions or shorter descriptions?

What is the average ratings for newer apps?

Which genre of apps are highly rated?
<br />

<h2 align="center">Combining datasets</h2>

Before we get start analyzing data we need to combine datasets so we have the full picture. For this we will be using sqliteonline.com as our SQL interpreter to easily write and execute SQL code online instead of going through the hassle of installing a software. One of the drawback of using sqliteoneline.com is that it can only handle a file size no greater than 4MB and the size of our dataset does surpass this limit so in order to utilize the data it was split into four smaller files and here we merged the four separate files back into one and named it "AppleStoreCombined."

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql1.png" />
</p>


<h4>SQL code:</h4>

```SQL
CREATE TABLE AppleStoreCombined AS
SELECT * FROM appleStore_description1
UNION ALL
SELECT * FROM appleStore_description2
UNION ALL
SELECT * FROM appleStore_description3
UNION ALL
SELECT * FROM appleStore_description4
``` 


<h2 align="center">Number of apps in each genre:</h2>

Based from the query results, Games, Entertainment, and Education are the genres with most number of apps.

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql2.png" />
</p>


<h4>SQL code:</h4>

```SQL
SELECT prime_genre AS Genre, COUNT (*) AS Number_of_Apps
FROM AppleStore
GROUP BY prime_genre
ORDER BY Number_of_Apps DESC
```

<h2 align="center">Range and average of apps' ratings:</h2>

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql3.png" />
</p>

<h4>SQL code:</h4>

```SQL
SELECT min(user_rating) AS MinimumRating,
       max(user_rating) AS MaximumRating,
       avg(user_rating) AS AverageRating
FROM AppleStore
```

<h2 align="center">Average ratings between free apps and paid apps:</h2>

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql4.png" />
</p>

<h4>SQL code:</h4>

```SQL
SELECT CASE
    WHEN price > 0 THEN 'Paid'
    ELSE 'Free'
    END AS Paid_or_Free,
    avg(user_rating) AS AverageRating
FROM AppleStore
GROUP BY Paid_or_Free  
```

<h2 align="center">Correlation between higher ratings and higher number of supported languages:</h2>

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql5.png" />
</p>

<h4>SQL code:</h4>

```SQL
SELECT CASE
    WHEN lang_num < 10 THEN '< 10 languages'
    WHEN lang_num BETWEEN 10 AND 30 THEN '10-30 languages'
    ELSE '> 30 languages'
    END AS NumberOfLanguages,
    avg(user_rating) AS AverageRating
FROM AppleStore
GROUP BY NumberOfLanguages
ORDER BY AverageRating DESC 
```


<h2 align="center">10 lowest rated genres:</h2>

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql6.png" />
</p>

<h4>SQL code:</h4>

```SQL
SELECT prime_genre AS Genre,
       avg(user_rating) AS AverageRating
FROM AppleStore
GROUP BY Genre
ORDER BY AverageRating ASC
LIMIT 10
```

<h2 align="center">Correlation between app description length and user rating:</h2>

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql7.png" />
</p>

<h4>SQL code:</h4>

```SQL
SELECT CASE
    WHEN length(ColumnB.app_desc) < 500 THEN 'Short'
    WHEN length(ColumnB.app_desc) BETWEEN 500 AND 1000 THEN 'Medium'
    ELSE 'Long'
    END AS DescriptionLength,
    avg(user_rating) AS AverageRating
FROM AppleStore AS ColumnA
JOIN AppleStoreCombined AS ColumnB
ON ColumnA.id = ColumnB.id
GROUP BY DescriptionLength
ORDER BY AverageRating DESC
```

<h2 align="center">Highest rated app from each genre:</h2>

<p align="center">
<img src="https://github.com/Mwajiduddin/Real-world-example-of-SQL-data-analysis/blob/main/sql8.png" />
</p>

<h4>SQL code:</h4>

```SQL
SELECT
    prime_genre AS Genre, 
    track_name AS App, 
    user_rating AS Rating 
FROM (
      SELECT
      prime_genre,
      track_name,
      user_rating,
      RANK() OVER (PARTITION BY prime_genre ORDER BY user_rating DESC, rating_count_tot DESC) AS Rank
      FROM AppleStore
     )  
     AS First
     WHERE First.Rank = 1
```

