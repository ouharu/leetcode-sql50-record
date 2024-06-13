https://leetcode.com/studyplan/top-sql-50/

> 打算6/10-6/16一星期时间刷完的,6/12了刷题进度9/50，特在小红书打卡来激励自己。
主要记录一下做了什么题，思路（这些用typora写成.md，然后push到github，内容复制到小红书）
 #leetcode[话题]#  #sql[话题]#  #sql刷题[话题]# 

>没想到pandas实现sql一样的功能竟然有这么多坑：1280题，解法2 pandas需考虑student_name中是否存在null，左连接需要新增列记录考试出勤情况，不然就不会出现null，最后还要还原缺省值；解法1 新增列语法agg记忆难度大，同时列名被打乱需要重新排序。综上，个人不熟悉pandas的情况下为提高解题速度leetcode sql50 join section 从1280后没有pandas解答

- [1378. Replace Employee ID With The Unique Identifier](#1378-replace-employee-id-with-the-unique-identifier)
  - [sql](#sql)
  - [pandas](#pandas)
- [1068. Product Sales Analysis I](#1068-product-sales-analysis-i)
  - [sql](#sql-1)
  - [pandas](#pandas-1)
- [595. Big Countries](#595-big-countries)
  - [sql](#sql-2)
  - [pandas](#pandas-2)
- [197. Rising Temperature](#197-rising-temperature)
  - [sql](#sql-3)
  - [pandas](#pandas-3)
- [1661. Average Time of Process per Machine](#1661-average-time-of-process-per-machine)
  - [sql](#sql-4)
  - [pandas](#pandas-4)
- [577. Employee Bonus](#577-employee-bonus)
  - [sql](#sql-5)
  - [pandas](#pandas-5)
- [1280. Students and Examinations](#1280-students-and-examinations)
  - [sql](#sql-6)
  - [pandas](#pandas-6)
- [570. Managers with at Least 5 Direct Reports](#570-managers-with-at-least-5-direct-reports)
  - [sql](#sql-7)
- [1934. Confirmation Rate](#1934-confirmation-rate)
  - [sql](#sql-8)


# 1378. Replace Employee ID With The Unique Identifier
https://leetcode.com/problems/replace-employee-id-with-the-unique-identifier/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 join as on
select 
eu.unique_id , e.name
from 
employees as e
left join
employeeuni as eu
on 
e.id = eu.id
;
```
## pandas

```python
import pandas as pd

def replace_employee_id(employees: pd.DataFrame, employee_uni: pd.DataFrame) -> pd.DataFrame:
    # 使用 merge 方法进行左连接
    result = pd.merge(employees, employee_uni, on='id', how='left')

    # 选择所需的列
    final_result = result[['unique_id', 'name']]

    return final_result
```

# 1068. Product Sales Analysis I
https://leetcode.com/problems/product-sales-analysis-i/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 同上
select 
pt.product_name, st.year, st.price
from 
sales as st
left join
product as pt
on
st.product_id = pt.product_id;
```
## pandas

```python
import pandas as pd

def sales_analysis(sales: pd.DataFrame, product: pd.DataFrame) -> pd.DataFrame:
    # 使用 merge 方法进行左连接
    result = pd.merge(sales, product, on='product_id', how='left')

    # 选择所需的列
    final_result = result[['product_name', 'year', 'price']]

    return final_result
```

# 595. Big Countries
https://leetcode.com/problems/big-countries/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 join-where-groupby , 没有新知识点
SELECT 
V.CUSTOMER_ID, COUNT(*) AS COUNT_NO_TRANS
FROM 
VISITS AS V
LEFT JOIN
TRANSACTIONS AS T
ON 
V.VISIT_ID = T.VISIT_ID
WHERE
T.TRANSACTION_ID IS NULL
GROUP BY V.CUSTOMER_ID; -- 此处与select column_name一致
```
## pandas

```python
import pandas as pd

def find_customers(visits: pd.DataFrame, transactions: pd.DataFrame) -> pd.DataFrame:
    # 使用 merge 方法进行左连接
    merged = pd.merge(visits,transactions,how='left', on='visit_id')            
    
    # 筛选出 TRANSACTION_ID 为空的行
    no_transactions = merged[merged['transaction_id'].isnull()]

    # 使用 groupby 和 size 方法进行分组计数，并重命名列
    result = no_transactions.groupby('customer_id').size().reset_index(name='count_no_trans')
    return result
```

# 197. Rising Temperature
https://leetcode.com/problems/rising-temperature/?envType=study-plan-v2&envId=top-sql-50
## sql

```sql
-- 考察 self-join and date_add
select w1.id
from 
weather as w1
left join
weather as w2
on
w1.recorddate = DATE_ADD(w2.recordDate, INTERVAL 1 DAY)
where 
w1.temperature > w2.temperature
;
```
## pandas

```python
import pandas as pd

def rising_temperature(weather: pd.DataFrame) -> pd.DataFrame:
    
    # 确保 recordDate 是 datetime 类型
    weather['recordDate'] = pd.to_datetime(weather['recordDate'])

    # 自连接 DataFrame，使用日期偏移量匹配
    merged = pd.merge(
        weather,
        weather,
        left_on='recordDate',
        right_on=weather['recordDate'] + pd.Timedelta(days=1),
        suffixes=('_w1', '_w2')
    )

    # 筛选出符合条件的行
    filtered = merged[merged['temperature_w1'] > merged['temperature_w2']]

    # 选择 w1 的 id 列
    result = filtered[['id_w1']].rename(columns={'id_w1': 'id'})

    return result    
```

# 1661. Average Time of Process per Machine
https://leetcode.com/problems/average-time-of-process-per-machine/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察self join, avg, round, groupby
select 
a1.machine_id, round(avg(a2.timestamp-a1.timestamp), 3) as processing_time 
from 
Activity a1
left join 
Activity a2 
on 
a1.machine_id=a2.machine_id and a1.process_id=a2.process_id
and a1.activity_type='start' and a2.activity_type='end'
group by 
a1.machine_id
;
```
## pandas

```python
import pandas as pd

def get_average_time(activity: pd.DataFrame) -> pd.DataFrame:
    # 自连接 DataFrame，使用条件匹配
    merged = activity.merge(activity, on=['machine_id', 'process_id'], suffixes=('_start', '_end'))
    merged = merged[(merged['activity_type_start'] == 'start') & (merged['activity_type_end'] == 'end')]

    # 计算每个进程的处理时间
    merged['processing_time'] = merged['timestamp_end'] - merged['timestamp_start']

    # 按 machine_id 分组并计算平均处理时间
    result = merged.groupby('machine_id')['processing_time'].mean().reset_index()

    # 保留三位小数
    result['processing_time'] = result['processing_time'].round(3)

    return result[['machine_id','processing_time']]
```

# 577. Employee Bonus
https://leetcode.com/problems/employee-bonus/?envType=study-plan-v2&envId=top-sql-50

## sql
```sql
-- 考察join-where-or
select
e.name,b.bonus
from 
employee as e
left join
bonus as b
on
e.empid = b.empid
where
b.bonus < 1000 or b.bonus is null
;
```
## pandas
```python
import pandas as pd

def employee_bonus(employee: pd.DataFrame, bonus: pd.DataFrame) -> pd.DataFrame:
    merged = pd.merge(employee,bonus,how='left',on='empId')
    filter = merged[(merged['bonus']<1000) | (merged['bonus'].isnull())]
    return filter[['name','bonus']]
```

# 1280. Students and Examinations

## sql
```sql
-- 考察 coalesce，cross-join, group-by, order-by
SELECT 
    s.student_id,
    s.student_name,
    sub.subject_name,
    COALESCE(COUNT(e.student_id), 0) AS attended_exams
FROM 
    Students s
CROSS JOIN 
    Subjects sub
LEFT JOIN 
    Examinations e
ON 
    s.student_id = e.student_id AND 
    sub.subject_name = e.subject_name
GROUP BY 
    s.student_id, s.student_name, sub.subject_name
ORDER BY 
    s.student_id, sub.subject_name;
```
## pandas

```python
# 解法1
import pandas as pd

def students_and_examinations(students: pd.DataFrame, subjects: pd.DataFrame, examinations: pd.DataFrame) -> pd.DataFrame:
    # 笛卡儿积
    left = pd.merge(students,subjects,how='cross')
    
    # group-by 
    sector = examinations.groupby(['student_id','subject_name'],as_index=False)
    # right df 3列 id、name、count
    right = sector.agg(attended_exams=('subject_name','count'))
    # 左结合
    df = pd.merge(left,right,how='left',on=['student_id' , 'subject_name'])
    # 填充null = 0
    df['attended_exams'].fillna(0,inplace=True)
    # 按列排序
    df = df.sort_values(by=['student_id','subject_name'])

    df = df[['student_id' , 'student_name' , 'subject_name' , 'attended_exams']]

    return df

# 解法2
import pandas as pd

def students_and_examinations(students: pd.DataFrame, subjects: pd.DataFrame, examinations: pd.DataFrame) -> pd.DataFrame:
    # 填充缺失值
    students['student_name'] = students['student_name'].fillna('null')
    
    # 笛卡尔积（cross join）
    merged_df = pd.merge(students, subjects, how='cross')
    
    # 左连接（left join）
    examinations['attended_exams'] = 1
    merged_df = pd.merge(merged_df, examinations, on=['student_id','subject_name'], how='left')
    
    # 计算考试次数并填充缺失值
    merged_df['attended_exams'] = merged_df['attended_exams'].fillna(0)
    
    # 按前三列分组计算考试次数的和
    merged_df = merged_df.groupby(['student_id','student_name','subject_name']).sum().reset_index()
    merged_df.loc[merged_df['student_name']=='null', 'student_name'] = np.nan
    return merged_df 
```

# 570. Managers with at Least 5 Direct Reports

## sql
```sql
-- 考察 in, group-by, having
-- 方法1： inner join
-- select
--     name
-- from
--     employee as e
-- inner join
-- (select 
--     managerID,count(managerId) as cid
-- from
--     employee
-- group by
--     managerId
-- having
--     count(managerId)>=5
-- ) as c
-- on
--     e.id = c.managerID
-- ;

-- 方法2：
SELECT 
    name 
FROM 
    Employee 
WHERE 
    id IN (
    SELECT 
        managerId 
    FROM 
        Employee 
    GROUP BY 
        managerId 
    HAVING 
        COUNT(*) >= 5
    )
```

# 1934. Confirmation Rate
## sql

```sql
-- 考察 if, left-join,on,group-by
select
    s.user_id, round(avg(if(c.action='confirmed',1.00,0.00)),2) as confirmation_rate
from
    signups as s
left join
    confirmations as c
on 
    s.user_id = c.user_id
group by
    s.user_id
;
```