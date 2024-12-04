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


 
