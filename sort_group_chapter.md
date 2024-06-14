<https://leetcode.com/studyplan/top-sql-50/>

> 打算6/10-6/16一星期时间刷完的,6/12了刷题进度9/50，特在小红书打卡来激励自己。
主要记录一下做了什么题，思路（这些用typora写成.md，然后push到github，内容复制到小红书）
 #leetcode[话题]#  #sql[话题]#  #sql刷题[话题]#

- [2356. Number of Unique Subjects Taught by Each Teacher](#2356-number-of-unique-subjects-taught-by-each-teacher)
  - [sql](#sql)
- [1141. User Activity for the Past 30 Days I](#1141-user-activity-for-the-past-30-days-i)
  - [sql](#sql-1)
- [1070. Product Sales Analysis III](#1070-product-sales-analysis-iii)
  - [sql](#sql-2)
- [596. Classes More Than 5 Students](#596-classes-more-than-5-students)
  - [sql](#sql-3)
- [1729. Find Followers Count](#1729-find-followers-count)
  - [sql](#sql-4)
- [619. Biggest Single Number](#619-biggest-single-number)
  - [sql](#sql-5)
- [1045. Customers Who Bought All Products](#1045-customers-who-bought-all-products)
  - [sql](#sql-6)

# 2356. Number of Unique Subjects Taught by Each Teacher

## sql

```sql
-- 考察 count-distinct-groupby
select
    teacher_id,
    count(distinct subject_id) as cnt
from
    teacher
group by
    teacher_id
;
```

# 1141. User Activity for the Past 30 Days I

## sql

```sql
-- 考察 count(distinct) date_sub('',interval xx day)
select
    activity_date as day,
    count(distinct user_id) as active_users
from 
    activity
where
    activity_date > date_sub('2019-07-27',interval 30 day)  and 
    activity_date <= '2019-07-27'
group by 
    activity_date;
```

# 1070. Product Sales Analysis III

## sql

```sql
-- 考察 self-join
select 
    s.product_id,
    s.year as first_year,
    s.quantity,
    s.price
from
    sales as s
inner join
(select 
    product_id,
    min(year) as first_year
from 
    sales
group by
    product_id
) as p
on
    s.product_id = p.product_id and
    s.year = p.first_year
;
```

# 596. Classes More Than 5 Students

## sql

```sql
-- 考察 group-by, having
select 
    class
from
    courses
group by
    class
having 
    count(class) >= 5
;
```

# 1729. Find Followers Count

## sql

```sql
-- 考察 groupby-orderby
select
    user_id,
    count(follower_id) as followers_count 
from
    followers
group by
    user_id
order by
    user_id
;
```

# 619. Biggest Single Number

## sql

```sql
-- 考察 where in  subquery
-- method 1
select 
    max(num)as num 
from 
    mynumbers 
where 
    num 
in (
    select 
        num 
    from 
        mynumbers 
    group by 
        num 
    having 
        count(num)=1 
);
-- method 2
select 
    max(num) as num 
from 
   (
    select 
        num 
    from 
        mynumbers 
    group by 
        num 
    having 
        count(num)=1 
    ) as m
;

```

# 1045. Customers Who Bought All Products

## sql

```sql
-- 考察 groupby-having-count=count
select
    customer_id
from
    customer
group by
    customer_id
having
    count(distinct product_key) = (select count(product_key) from product)
;
```
