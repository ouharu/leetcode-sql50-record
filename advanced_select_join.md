<https://leetcode.com/studyplan/top-sql-50/>

> 打算6/10-6/16一星期时间刷完的,6/12了刷题进度9/50，特在小红书打卡来激励自己。
主要记录一下做了什么题，思路（这些用typora写成.md，然后push到github，内容复制到小红书）
 #leetcode[话题]#  #sql[话题]#  #sql刷题[话题]#

- [1731. The Number of Employees Which Report to Each Employee](#1731-the-number-of-employees-which-report-to-each-employee)
  - [sql](#sql)
- [1789. Primary Department for Each Employee](#1789-primary-department-for-each-employee)
  - [sql](#sql-1)
- [610. Triangle Judgement](#610-triangle-judgement)
  - [sql](#sql-2)
- [180. Consecutive Numbers](#180-consecutive-numbers)
  - [sql](#sql-3)
- [1164. Product Price at a Given Date](#1164-product-price-at-a-given-date)
  - [sql](#sql-4)
- [1204. Last Person to Fit in the Bus](#1204-last-person-to-fit-in-the-bus)
  - [sql](#sql-5)
- [1907. Count Salary Categories](#1907-count-salary-categories)
  - [sql](#sql-6)

# 1731. The Number of Employees Which Report to Each Employee

## sql

```sql
-- 考察 join-where-groupby-orderby
select 
    a.reports_to as employee_id,
    b.name,
    count(a.reports_to) as reports_count,
    round(avg(a.age)) as average_age
from
    employees as a
inner join
    employees as b
on 
    a.reports_to = b.employee_id
where
    a.reports_to is not null
group by
    a.reports_to
order by
    a.reports_to
;
```

# 1789. Primary Department for Each Employee

## sql

```sql
-- 考察 where in / join where groupby having
SELECT 
    e.employee_id,
    e.department_id
FROM 
    Employee e
LEFT JOIN 
    Employee e2
ON 
    e.employee_id = e2.employee_id AND e2.primary_flag = 'Y'
WHERE 
    (e.primary_flag = 'Y') OR 
    (e2.employee_id IS NULL)
group by
    e.employee_id
having 
    count(e.employee_id) = 1    
;
-- method 2
select 
    employee_id,
    department_id 
from 
    Employee 
where 
    primary_flag = 'Y' or employee_id in (select employee_id from Employee group by employee_id having count(employee_id)=1);
```

# 610. Triangle Judgement

## sql

```sql
-- 考察 if
select 
    x,y,z,
    if(x+y>z and x+z>y and z+y>x,'Yes','No') as triangle
from
    triangle
;
```

# 180. Consecutive Numbers

## sql

```sql
-- 考察 复合where join
select
    distinct l1.num as ConsecutiveNums
from
    logs as l1,
    logs as l2,
    logs as l3
where
    l1.id = l2.id-1 and
    l1.id = l3.id-2 and
    l1.num = l2.num and
    l1.num = l3.num
;
```

# 1164. Product Price at a Given Date

## sql

```sql
-- 考察 groupby-orderby
select 
    distinct product_id,
    10 as price
from 
    products
group by
    product_id
having
    min(change_date) > "2019-08-16"

union

select 
    product_id, 
    new_price
from 
    Products 
where 
    (product_id, change_date) in(
                                select 
                                    product_id, 
                                    max(change_date) as recent_date
                                from 
                                    Products
                                where 
                                    change_date <= "2019-08-16"
                                group by 
                                    product_id
                                )
```

# 1204. Last Person to Fit in the Bus

## sql

```sql
-- 考察 subquery,func() over(order by column)窗口函数
SELECT person_name
FROM (
    SELECT 
        person_name,
        SUM(weight) OVER (ORDER BY turn) AS cumulative_weight
    FROM 
        Queue
) sub
WHERE 
    cumulative_weight <= 1000
ORDER BY 
    cumulative_weight DESC
LIMIT 1;

;

```

# 1907. Count Salary Categories

## sql

```sql
-- 考察 union
select
    'Low Salary' as category,
    count(income) as accounts_count
from
    accounts
where 
    income < 20000
union
select
    'Average Salary' as category,
    count(income) as accounts_count
from
    accounts
where 
    income >= 20000 and income <=50000
union
select
    'High Salary' as category,
    count(income) as accounts_count
from
    accounts
where 
    income > 50000
;
```
