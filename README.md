## 极客时间大数据训练营作业3

### 1.查看数据源

数据源位置

![image-20220320225633696](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220320225633696.png)



Users数据源格式

![image-20220320234055875](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220320234055875.png)



movies数据源格式

![image-20220320234245141](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220320234245141.png)





Ratings数据源格式

![image-20220320234330653](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220320234330653.png)



### 2.创建库和表-外部表

#### 创建数据库afa

![image-20220320231049960](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220320231049960.png)



#### 创建 t_user 观众表

- 字段为：UserID, Sex, Age, Occupation, Zipcode
- 字段中文解释：用户 id，性别，年龄，职业，邮编

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS afa.t_user(
  userid INT,
  sex STRING,
  age INT,
  occupation INT,
  zipcode INT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ('field.delim'='::')
LOCATION '/data/hive/users';
```

#### 创建 t_movie 电影表

- 字段为：MovieID, MovieName, MovieType
- 字段中文解释：电影 ID，电影名，电影类型

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS afa.t_movie(
  movieid INT ,
  moviename STRING ,
  movietype STRING 
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ('field.delim'='::')
LOCATION '/data/hive/movies';

```

#### 创建 t_rating 影评表

- 字段为：UserID, MovieID, Rate, Times
- 字段中文解释：用户 ID，电影 ID，评分，评分时间

```sql
CREATE EXTERNAL TABLE IF NOT EXISTS afa.t_rating(
  userid INT,
  movieid INT,
  rate INT,
  times BIGINT
)
ROW FORMAT SERDE 'org.apache.hadoop.hive.contrib.serde2.MultiDelimitSerDe'
WITH SERDEPROPERTIES ('field.delim'='::')
LOCATION '/data/hive/ratings';
```

### 3.题目1

展示电影 ID 为 2116 这部电影各年龄段的平均影评分。

```sql
select a.age, avg(b.rate) from t_user a join (select userid,rate from t_rating where movieid=2116) b ON a.userid = b.userid group by a.age;
```

运行结果：

![image-20220322194653568](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220322194653568.png)



### 4.题目2







### 5.题目3

思路先找评论最多的女士， 然后找到该女士评分最高的10部电影 ，然后计算这10部电影的名称和平均影评分。

```sql
# 找到评论最多的女士
select  a.userid, count(a.userid) from t_rating a join t_user b on a.userid = b.userid and b.sex = 'F' group by a.userid order by count(a.userid) desc limit 1;
```



```sql
# 该女士评分最高的10部电影
select a1.movieid as movieid from t_rating a1 join  (select  a.userid, count(a.userid) from t_rating a join t_user b on a.userid = b.userid and b.sex = 'F' group by a.userid order by count(a.userid) desc limit 1) b1 on a1.userid = b1.userid order by a1.rate desc limit 10;

1256
1094
905
2064
2997
750
904
1236
1279
745
```

运行结果：

![image-20220322204553981](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220322204553981.png)



```sql
# 计算10部电影的平均分
select b2.moviename, avg(a2.rate) from t_rating  a2 join t_movie b2  on a2.movieid = b2.movieid where a2.movieid in (
1256,
1094,
905,
2064,
2997,
750,
904,
1236,
1279,
745) group by b2.moviename order by avg(a2.rate) desc ;


```

运行结果：

![image-20220322205005041](https://typora-mac-alpha.oss-cn-shanghai.aliyuncs.com/img/image-20220322205005041.png)

### 

