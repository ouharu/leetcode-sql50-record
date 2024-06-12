https://leetcode.com/studyplan/top-sql-50/

> 打算6/10-6/16一星期时间刷完的,6/12了刷题进度9/50，特在小红书打卡来激励自己。
主要记录一下做了什么题，思路（这些用typora写成.md，然后push到github，内容复制到小红书）
 #leetcode[话题]#  #sql[话题]#  #sql刷题[话题]# 

- [1757. Recyclable and Low Fat Products](#1757-recyclable-and-low-fat-products)
  - [sql](#sql)
  - [pandas](#pandas)
- [584. Find Customer Referee](#584-find-customer-referee)
  - [sql](#sql-1)
  - [pandas](#pandas-1)
- [595. Big Countries](#595-big-countries)
  - [sql](#sql-2)
  - [pandas](#pandas-2)
- [1148. Article Views I](#1148-article-views-i)
  - [sql](#sql-3)
  - [pandas](#pandas-3)
- [1683. Invalid Tweets](#1683-invalid-tweets)
  - [sql](#sql-4)
  - [pandas](#pandas-4)


# 1757. Recyclable and Low Fat Products
https://leetcode.com/problems/recyclable-and-low-fat-products/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 select-where
select 
    product_id 
from 
    Products
where 
    low_fats = 'Y'
and
    recyclable = 'Y'
;
```
## pandas

```python
import pandas as pd

def find_products(products: pd.DataFrame) -> pd.DataFrame:
    # condition
    df = products[(products['low_fats']=='Y')&(products['recyclable']=='Y')]
    # Each column in a DataFrame is a Series. As a single column is selected, the returned object is a pandas Series: df = df['product_id']
    df = df[['product_id']] # returned type: DataFrames
    return df

```

# 584. Find Customer Referee
https://leetcode.com/problems/find-customer-referee/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 select-where- <> / is null
select 
    name
from
    Customer
where
    referee_id<>2
or
    referee_id is null
;
```
## pandas

```python
import pandas as pd

def find_customer_referee(customer: pd.DataFrame) -> pd.DataFrame:
    # != .isnull()
    df = customer[(customer['referee_id']!=2)|(customer['referee_id'].isnull())]
    return df[['name']]
```

# 595. Big Countries
https://leetcode.com/problems/big-countries/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 select-where , 没有新知识点
select
    name,
    population,
    area
from
    World
where
    area >= 3000000
or
    population >= 25000000
;
```
## pandas

```python
import pandas as pd

def big_countries(world: pd.DataFrame) -> pd.DataFrame:
    res = world[(world['area']>=3000000) | (world['population']>=25000000)]
    return res[['name','population','area']]
```

# 1148. Article Views I
https://leetcode.com/problems/article-views-i/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 distinct as order-by
select 
distinct author_id as id 
from 
views
where 
author_id = viewer_id
order by 
author_id;
```
## pandas

```python
import pandas as pd

def article_views(views: pd.DataFrame) -> pd.DataFrame:
    
    # 步骤1: 筛选 author_id 等于 viewer_id 的行
    filtered_views = views[views['author_id'] == views['viewer_id']]

    # 步骤2: 删除 author_id 列中的重复值
    unique_authors = filtered_views.drop_duplicates(subset='author_id')

    # 步骤3: 选择 author_id 列并重命名为 id
    result = unique_authors[['author_id']].rename(columns={'author_id': 'id'})

    # 步骤4: 按 id 列排序
    sorted_result = result.sort_values(by='id',ascending=True)
    return sorted_result    
```

# 1683. Invalid Tweets
https://leetcode.com/problems/invalid-tweets/?envType=study-plan-v2&envId=top-sql-50

## sql

```sql
-- 考察 length
select tweet_id from tweets
where length(content)>15;
```
## pandas

```python
import pandas as pd

def invalid_tweets(tweets: pd.DataFrame) -> pd.DataFrame:
    len_tweets = tweets[tweets['content'].str.len() > 15]
    return len_tweets[['tweet_id']]
```