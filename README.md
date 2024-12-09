# Netflix Movies and TV Shows Data Analysis Using SQL
![Netflix Logo](https://github.com/shobhitsvt/Netflix-Sql-Project/blob/main/Netflix%20img.avif)
Overview
This project involves a comprehensive analysis of Netflix's movies and TV shows data using SQL. The goal is to extract valuable insights and answer various business questions based on the dataset. The following README provides a detailed account of the project's objectives, business problems, solutions, findings, and conclusions.

Objectives
Analyze the distribution of content types (movies vs TV shows).
Identify the most common ratings for movies and TV shows.
List and analyze content based on release years, countries, and durations.
Explore and categorize content based on specific criteria and keywords.
Dataset
The data for this project is sourced from the Kaggle dataset:

Dataset Link: Movies Dataset
Schema
DROP TABLE IF EXISTS netflix;
CREATE TABLE netflix
(
    show_id      VARCHAR(5),
    type         VARCHAR(10),
    title        VARCHAR(250),
    director     VARCHAR(550),
    casts        VARCHAR(1050),
    country      VARCHAR(550),
    date_added   VARCHAR(55),
    release_year INT,
    rating       VARCHAR(15),
    duration     VARCHAR(15),
    listed_in    VARCHAR(250),
    description  VARCHAR(550)
);
Business Problems and Solutions
1. Count the Number of Movies vs TV Shows
SELECT 
    type,
    COUNT(*)
FROM netflix
GROUP BY 1;
Objective: Determine the distribution of content types on Netflix.

2. Find the Most Common Rating for Movies and TV Shows
WITH RatingCounts AS (
    SELECT 
        type,
        rating,
        COUNT(*) AS rating_count
    FROM netflix
    GROUP BY type, rating
),
RankedRatings AS (
    SELECT 
        type,
        rating,
        rating_count,
        RANK() OVER (PARTITION BY type ORDER BY rating_count DESC) AS rank
    FROM RatingCounts
)
SELECT 
    type,
    rating AS most_frequent_rating
FROM RankedRatings
WHERE rank = 1;
Objective: Identify the most frequently occurring rating for each type of content.

3. List All Movies Released in a Specific Year (e.g., 2020)
SELECT * 
FROM netflix
WHERE release_year = 2020;
Objective: Retrieve all movies released in a specific year.

4. Find the Top 5 Countries with the Most Content on Netflix
SELECT * 
FROM
(
    SELECT 
        UNNEST(STRING_TO_ARRAY(country, ',')) AS country,
        COUNT(*) AS total_content
    FROM netflix
    GROUP BY 1
) AS t1
WHERE country IS NOT NULL
ORDER BY total_content DESC
LIMIT 5;
Objective: Identify the top 5 countries with the highest number of content items.

5. Identify the Longest Movie
SELECT 
    *
FROM netflix
WHERE type = 'Movie'
ORDER BY SPLIT_PART(duration, ' ', 1)::INT DESC;
Objective: Find the movie with the longest duration.

6. Find Content Added in the Last 5 Years
SELECT *
FROM netflix
WHERE TO_DATE(date_added, 'Month DD, YYYY') >= CURRENT_DATE - INTERVAL '5 years';
Objective: Retrieve content added to Netflix in the last 5 years.

7. Find All Movies/TV Shows by Director 'Rajiv Chilaka'
SELECT *
FROM (
    SELECT 
        *,
        UNNEST(STRING_TO_ARRAY(director, ',')) AS director_name
    FROM netflix
) AS t
WHERE director_name = 'Rajiv Chilaka';
Objective: List all content directed by 'Rajiv Chilaka'.

8. List All TV Shows with More Than 5 Seasons
SELECT *
FROM netflix
WHERE type = 'TV Show'
  AND SPLIT_PART(duration, ' ', 1)::INT > 5;
Objective: Identify TV shows with more than 5 seasons.

9. Count the Number of Content Items in Each Genre
SELECT 
    UNNEST(STRING_TO_ARRAY(listed_in, ',')) AS genre,
    COUNT(*) AS total_content
FROM netflix
GROUP BY 1;
Objective: Count the number of content items in each genre.

10.Find each year and the average numbers of content release in India on netflix.
return top 5 year with highest avg content release!

SELECT 
    country,
    release_year,
    COUNT(show_id) AS total_release,
    ROUND(
        COUNT(show_id)::numeric /
        (SELECT COUNT(show_id) FROM netflix WHERE country = 'India')::numeric * 100, 2
    ) AS avg_release
FROM netflix
WHERE country = 'India'
GROUP BY country, release_year
ORDER BY avg_release DESC
LIMIT 5;
Objective: Calculate and rank years by the average number of content releases by India.

11. List All Movies that are Documentaries
SELECT * 
FROM netflix
WHERE listed_in LIKE '%Documentaries';
Objective: Retrieve all movies classified as documentaries.

12. Find All Content Without a Director
SELECT * 
FROM netflix
WHERE director IS NULL;
Objective: List content that does not have a director.

13. Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years
SELECT * 
FROM netflix
WHERE casts LIKE '%Salman Khan%'
  AND release_year > EXTRACT(YEAR FROM CURRENT_DATE) - 10;
Objective: Count the number of movies featuring 'Salman Khan' in the last 10 years.

14. Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India
SELECT 
    UNNEST(STRING_TO_ARRAY(casts, ',')) AS actor,
    COUNT(*)
FROM netflix
WHERE country = 'India'
GROUP BY actor
ORDER BY COUNT(*) DESC
LIMIT 10;
Objective: Identify the top 10 actors with the most appearances in Indian-produced movies.

15. Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords
SELECT 
    category,
    COUNT(*) AS content_count
FROM (
    SELECT 
        CASE 
            WHEN description ILIKE '%kill%' OR description ILIKE '%violence%' THEN 'Bad'
            ELSE 'Good'
        END AS category
    FROM netflix
) AS categorized_content
GROUP BY category;
Objective: Categorize content as 'Bad' if it contains 'kill' or 'violence' and 'Good' otherwise. Count the number of items in each category.

Findings and Conclusion
Content Distribution: The dataset contains a diverse range of movies and TV shows with varying ratings and genres.
Common Ratings: Insights into the most common ratings provide an understanding of the content's target audience.
Geographical Insights: The top countries and the average content releases by India highlight regional content distribution.
Content Categorization: Categorizing content based on specific keywords helps in understanding the nature of content available on Netflix.
This analysis provides a comprehensive view of Netflix's content and can help inform content strategy and decision-making.

Author - Zero Analyst
This project is part of my portfolio, showcasing the SQL skills essential for data analyst roles. If you have any questions, feedback, or would like to collaborate, feel free to get in touch!

Stay Updated and Join the Community
For more content on SQL, data analysis, and other data-related topics, make sure to follow me on social media and join our community:

YouTube: Subscribe to my channel for tutorials and insights
Instagram: Follow me for daily tips and updates
LinkedIn: Connect with me professionally
Discord: Join our community to learn and grow together
Thank you for your support, and I look forward to connecting with you!

### 1. Count the Number of Movies vs TV Shows

select count(case when type='Movie' then 1 end) as Moviecount,
count(case when type='TV Show' then 1 end) as TV_Show
from Netflix

### 2. Find the Most Common Rating for Movies and TV Shows

with cte as(
select type, rating, count(*) as rating_count
from Netflix
group by type, rating),
cte2 as(
select type,rating, rating_count, row_number() over(partition by type order by rating_count desc) as rank
from cte)
select type, rating as Frequent_rating
from cte2
where rank=1

### 3. List All Movies Released in a Specific Year (e.g., 2020)

select*from Netflix
where release_year =2020

### 4. Find the Top 5 Countries with the Most Content on Netflix

select top 5 country, count(*) as total_content
from(
select value as country
from Netflix
cross apply string_split(country, ','))
as T1
where country is not null
group by country
order by total_content desc

### 5. Identify the Longest Movie

select*from Netflix
where type = 'Movie'
order by cast(substring(duration,1,CHARINDEX(' ',duration)-1) as Int) desc

### 6.Find Content Added in the Last 5 Years

select*from Netflix
where CONVERT(date,date_added,101)>= DATEADD(year,-5,GETDATE())

### 7.Find All Movies/TV Shows by Director 'Rajiv Chilaka'

select*from
(select*,value as director_name
from Netflix
cross apply string_split(director, ',') as director_name)as t
where director_name='Rajiv Chilaka'

### 8.List All TV Shows with More Than 5 Seasons

select*from Netflix
where type='TV Show'
and Cast(substring(duration, 1, charindex(' ', duration)- 1) as int)>5

### 9.Count the Number of Content Items in Each Genre

select value as genre, count(*) as total_content
from netflix
cross apply string_split(listed_in, ',')
group by value

### 10.Find each year and the average numbers of content release in India on netflix.

with cte as(
select count(show_id) as total_India_shows
from Netflix
where country='India')
select country, release_year,count(show_id) as total_release,
round(cast(count(show_id) as float)/CAST(total_India_shows as float)*100,2)as avg_Release
from Netflix
cross join cte
where country='India'
group by country, release_year, total_India_shows
order by avg_Release Desc
Offset 0 rows fetch next 5 rows only

### 11.List All Movies that are Documentaries

select*from Netflix
where listed_in like '%Documentaries'

### 12.Find All Content Without a Director

select*from Netflix
where director is null

### 13.Find How Many Movies Actor 'Salman Khan' Appeared in the Last 10 Years

select*from Netflix
where casts like '%Salman Khan%'
and release_year> year(getdate())-10

### 14.Find the Top 10 Actors Who Have Appeared in the Highest Number of Movies Produced in India

select top 10 value as actor, count(*) as total_appearances 
from Netflix
cross apply string_split(cast, ',')
where country ='India'
group by value
Order by total_appearances desc

### 15.Categorize Content Based on the Presence of 'Kill' and 'Violence' Keywords

select category, count(*) as content_Count from
(select case when description like '%kill%' or description like '%Violence%' then 'Bad' else 'Good' end as Category
from Netflix)as ss
group by category






















 
