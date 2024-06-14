<https://leetcode.com/studyplan/top-sql-50/>

> 打算6/10-6/16一星期时间刷完的,6/12了刷题进度9/50，特在小红书打卡来激励自己。
主要记录一下做了什么题，思路（这些用typora写成.md，然后push到github，内容复制到小红书）
 #leetcode[话题]#  #sql[话题]#  #sql刷题[话题]#

- [620. Not Boring Movies](#620-not-boring-movies)
  - [sql](#sql)
- [1251.Average Selling Price](#1251average-selling-price)
  - [sql](#sql-1)
- [1075. Project Employees I](#1075-project-employees-i)
  - [sql](#sql-2)
- [1633. Percentage of Users Attended a Contest](#1633-percentage-of-users-attended-a-contest)
  - [sql](#sql-3)
- [1211. Queries Quality and Percentage](#1211-queries-quality-and-percentage)
  - [sql](#sql-4)
- [1193. Monthly Transactions I](#1193-monthly-transactions-i)
  - [sql](#sql-5)
- [1174. Immediate Food Delivery II](#1174-immediate-food-delivery-ii)
  - [sql](#sql-6)
- [550. Game Play Analysis IV](#550-game-play-analysis-iv)
  - [sql](#sql-7)

# 620. Not Boring Movies

## sql

```sql
-- 考察 select-where-and-%-orderby-desc
select
    *
from
    cinema
where
    description <> 'boring' and
    id % 2 = 1
order by
    rating desc
;
```

# 1251.Average Selling Price

## sql

```sql
-- 考察 isnull()/coalesce(), between-and
select
    p.product_id, coalesce(round(sum(p.price*u.units)/sum(u.units),2),0) as average_price
from
    prices as p
left join
    unitssold as u
on
    p.product_id = u.product_id and
    u.purchase_date between 
    p.start_date and p.end_date
group by
    p.product_id
;
```

# 1075. Project Employees I

## sql

```sql
-- 考察 同上 , 没有新知识点
select 
    p.project_id, ifnull(round(avg(experience_years),2),0) as average_years
from
    project as p
left join
    employee as e
on
    p.employee_id = e.employee_id
group by
    p.project_id
;
```

# 1633. Percentage of Users Attended a Contest

## sql

```sql
select
-- 考察 distinct as order-by, 子查询
    r.contest_id , round(count(r.user_id)/(select count(user_id) from users),4)*100 as percentage
from
    register as r
group by
    r.contest_id
order by
    percentage desc , r.contest_id
;
```

# 1211. Queries Quality and Percentage

## sql

```sql
-- 考察 if(),having
select 
    query_name,
    round(avg(rating/position),2) as quality,
    round(avg(if(rating<3,1,0)),4)*100 as poor_query_percentage
from 
    queries
group by
    query_name
having
    query_name is not null
;
```

# 1193. Monthly Transactions I

## sql

```sql
-- 考察 sum(if()) date_formate(date,format)
select 
    DATE_FORMAT(trans_date, '%Y-%m') as month,
    country,
    count(amount) as trans_count,
    sum(if(state='approved',1,0)) as approved_count,
    sum(amount) as trans_total_amount,
    sum(if(state='approved',amount,0)) as approved_total_amount
from
    transactions
group by
    month , country
;
```

# 1174. Immediate Food Delivery II

## sql

```sql
-- 考察 select * from (select * from table) as t
select
    round(100*sum(d.immediate_count) / count(*),2) as immediate_percentage
from
(
select
    customer_id,
    if(min(order_date)=min(customer_pref_delivery_date),1,0) as immediate_count
from
    delivery
group by
    customer_id
) as d
;
```

# 550. Game Play Analysis IV

## sql

```sql
-- datediff, join
select 
    round(sum(if(datediff(a1.event_date,a2.start_date)=1,1,0))/count(distinct a1.player_id),2) as fraction
from
activity as a1
left join
(select
    player_id,
    min(event_date) as start_date
from
    activity
group by
    player_id
) as a2
on a1.player_id = a2.player_id
;
```
