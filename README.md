# Netflix-Sql-Project
## Business Problems and Solutions

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






















 
