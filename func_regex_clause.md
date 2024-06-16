<https://leetcode.com/studyplan/top-sql-50/>

> 打算6/10-6/16一星期时间刷完的,6/12了刷题进度9/50，特在小红书打卡来激励自己。
主要记录一下做了什么题，思路（这些用typora写成.md，然后push到github，内容复制到小红书）
 #leetcode[话题]#  #sql[话题]#  #sql刷题[话题]#

- [1667. Fix Names in a Table](#1667-fix-names-in-a-table)
  - [sql](#sql)
- [1527. Patients With a Condition](#1527-patients-with-a-condition)
  - [sql](#sql-1)
- [196. Delete Duplicate Emails](#196-delete-duplicate-emails)
  - [sql](#sql-2)
- [176. Second Highest Salary](#176-second-highest-salary)
  - [sql](#sql-3)
- [1484. Group Sold Products By The Date](#1484-group-sold-products-by-the-date)
  - [sql](#sql-4)
- [1327. List the Products Ordered in a Period](#1327-list-the-products-ordered-in-a-period)
  - [sql](#sql-5)
- [1517. Find Users With Valid E-Mails](#1517-find-users-with-valid-e-mails)
  - [sql](#sql-6)

# 1667. Fix Names in a Table

## sql

```sql
-- 考察 concat() upper() lower() substr()
select
    user_id,
    concat(upper(substr(name,1,1)),lower(substr(name,2))) as name
from
    users
order by
    user_id
; 
```

# 1527. Patients With a Condition

## sql

```sql
-- 考察regexp
select
    patient_id,
    patient_name,
    conditions 
from
    patients
where
    conditions regexp '\\bDIAB1'
;
```

# 196. Delete Duplicate Emails

## sql

```sql
-- 考察 delete join where

delete
    p1
from
    person as p1
join
    person as p2
where
    p1.email = p2.email
and
    p1.id > p2.id

```

# 176. Second Highest Salary

## sql

```sql
-- 考察 offset ifnull
SELECT 
    IFNULL(
        (SELECT DISTINCT Salary 
         FROM Employee
         ORDER BY Salary DESC 
         LIMIT 1 OFFSET 1),
        NULL
    ) AS SecondHighestSalary;

```

# 1484. Group Sold Products By The Date

## sql

```sql
-- 考察 group_concat() 
select
    sell_date,
    count(distinct product)  as num_sold,
    group_concat(distinct product order by product) as products 
from
    activities
group by
    sell_date;
;

```

# 1327. List the Products Ordered in a Period

## sql

```sql
-- 考察 join
select
    product_name,
    sum(unit) as unit
from
    orders as o
left join
    products as p
on 
    o.product_id = p.product_id
where
    order_date like '2020-02%'
group by
    o.product_id
having
    sum(unit) >= 100

```

# 1517. Find Users With Valid E-Mails

## sql

```sql
-- regexp
select
    user_id,
    name,
    mail
from
    users
where
    mail regexp '^[A-Za-z][A-Za-z0-9_.-]*@leetcode[.]com$'
```
