<https://leetcode.com/studyplan/top-sql-50/>

> 打算6/10-6/16一星期时间刷完的,6/12了刷题进度9/50，特在小红书打卡来激励自己。
主要记录一下做了什么题，思路（这些用typora写成.md，然后push到github，内容复制到小红书）
 #leetcode[话题]#  #sql[话题]#  #sql刷题[话题]#

- [1978. Employees Whose Manager Left the Company](#1978-employees-whose-manager-left-the-company)
  - [sql](#sql)
- [626. Exchange Seats](#626-exchange-seats)
  - [sql](#sql-1)
- [1341. Movie Rating](#1341-movie-rating)
  - [sql](#sql-2)
- [1321. Restaurant Growth](#1321-restaurant-growth)
  - [sql](#sql-3)
- [602. Friend Requests II: Who Has the Most Friends](#602-friend-requests-ii-who-has-the-most-friends)
  - [sql](#sql-4)
- [585. Investments in 2016](#585-investments-in-2016)
  - [sql](#sql-5)
- [185. Department Top Three Salaries](#185-department-top-three-salaries)
  - [sql](#sql-6)

# 1978. Employees Whose Manager Left the Company

## sql

```sql
-- 考察 where not in subquery
select 
    employee_id
from
    employees
where
    salary <30000
    and
    manager_id not in (select employee_id from employees)
order by
    employee_id
; 
```

# 626. Exchange Seats

## sql

```sql
-- 考察 case-when-then-end as column-name
select
    case 
        when id = (select max(id) from seat) and id%2=1
            then id
        when id%2=1
            then id+1
        when id%2=0
            then id-1
    end as id,
    student
from
    seat
order by
    id
;
```

# 1341. Movie Rating

## sql

```sql
-- 考察 select-from-join-on-where-groupby-orderby-limit-union all
(select 
    name as results
from
    users as u
left join
    movierating as m
on
    u.user_id = m.user_id
group by
    u.user_id
order by
    count(movie_id) desc,
    name 
limit 1
)
union all

(select 
    title as results
from
    movies as m1
left join
    movierating as m2
on
    m1.movie_id = m2.movie_id
where 
    created_at like '2020-02%'
group by
    m1.movie_id
order by
    avg(rating) desc,
    title
limit 1
)
```

# 1321. Restaurant Growth

## sql

```sql
-- 考察 over()
select 
    visited_on,
    amount,
    round(amount/7,2) as average_amount    
from
(SELECT 
    distinct visited_on, 
    -- 计算current date及前6️天之和，这里range保证有重复日期时前面的6天日期出现多次也能被计算
    SUM(amount) OVER(ORDER BY visited_on RANGE BETWEEN INTERVAL 6 DAY   PRECEDING AND CURRENT ROW) amount, 
    MIN(visited_on) OVER() 1st_date 
FROM 
    customer) as c
where 
    c.visited_on >= c.1st_date + 6;
```

# 602. Friend Requests II: Who Has the Most Friends

## sql

```sql
-- 考察 union all 
select
    r.id,
    count(r.id) as num
from
    (select
        requester_id as id
    from
        requestaccepted as ra
    union all
    select
        accepter_id
    from
        requestaccepted as ar
    ) as r
group by
    r.id
order by
    num desc
limit 1
;

```

# 585. Investments in 2016

## sql

```sql
-- 考察 where-in
select
    round(sum(tiv_2016),2) as tiv_2016
from
    insurance
where
    (
        tiv_2015 in
        (
            select tiv_2015 from insurance group by tiv_2015 having count(pid) >1
        )
    )
    and
    (
        (lat,lon) in
        (
            select lat,lon from insurance group by lat,lon having count(pid) =1
        )
    )
;

```

# 185. Department Top Three Salaries

## sql

```sql
-- 考察 over() subquery
# Write your MySQL query statement below
select 
    Department,
    Employee,
    salary
from
(select
    d.name as Department,
    e.name as Employee,
    salary,
    dense_rank() over(partition by d.name order by salary desc) as rnk
from
    employee as e
left join
    department as d
on
    e.departmentid = d.id
order by
    d.id , salary desc
) as rnk_tbl
where
    rnk <=3
;
```
