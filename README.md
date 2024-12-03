# Netflix-Sql-Project
## Business Problems and Solutions

### 1. Count the Number of Movies vs TV Shows
'''sql
 select count(case when type='Movie' then 1 end) as Moviecount,
 count(case when type='TV Show' then 1 end) as TV_Show
 from Netflix
 '''
