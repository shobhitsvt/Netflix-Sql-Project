# Netflix-Sql-Project
Count the Number of Movies vs TV Shows
select count(case when type='Movie' then 1 end) as Moviecount,
count(case when type='TV Show' then 1 end) as TV_Show
from Netflix
