# sql训练

#### -- 1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数	

###### -- 方案一

```sql
select a.* ,b.s_score as 01_score,c.s_score as 02_score 
from student a 
join score b on a.s_id=b.s_id and b.c_id='01'
left join score c on a.s_id=c.s_id and c.c_id='02' 
where b.s_score>c.s_score
```

###### -- 方案二

```sql
select a.*, b.s_score 01_score,c.s_score 02_score
from  student a
left join score b on a.s_id = b.s_id and b.c_id = 01
left join score c on a.s_id  = c.s_id and c.c_id = 02
where b.s_score > c.s_score;
```

#### -- 2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数

```sql
select a.*, b.s_score 01_score,c.s_score 02_score
from  student a
left join score b on a.s_id = b.s_id and b.c_id = 01
left join score c on a.s_id  = c.s_id and c.c_id = 02
where b.s_score < c.s_score;
```

#### -- 3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```sql
select  ROUND(avg(a.s_score)) avg,b.s_id,b.s_name
from score a 
left join student b on a.s_id = b.s_id
group by s_id having AVG(a.s_score) >=60;
```

#### -- 4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩(包括有成绩的和无成绩的)

###### -- 方案一

```sql
select
    S.s_id,
    S.s_name,
    round(avg(ifnull(C.s_score,0)),2) as avg_score
from
    Student S
left join Score C
on S.s_id = C.s_id
group by s_id
having avg_score < 60;
```

###### -- 方案二

```sql
select b.s_id,b.s_name,ROUND(AVG(a.s_score),2) as avg_score from 
	student b 
	left join score a on b.s_id = a.s_id
	GROUP BY b.s_id,b.s_name HAVING avg_score <60
	union
select a.s_id,a.s_name,0 as avg_score from 
	student a 
	where a.s_id not in (
				select distinct s_id from score);		
```

#### -- 5、查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

```sql
select a.s_id,a.s_name,count(b.c_id) as sum_course,sum(b.s_score) as sum_score from 
	student a 
	left join score b on a.s_id=b.s_id
	GROUP BY a.s_id,a.s_name;
```


​	



