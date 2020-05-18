### 1、查询"01"课程比"02"课程成绩高的学生的信息及课程分数	

###### -- 方案一

```sql
SELECT
	a.*,
	b.s_score AS 01 _score,
	c.s_score AS 02 _score 
FROM
	student a
	JOIN score b ON a.s_id = b.s_id 
	AND b.c_id = '01'
	LEFT JOIN score c ON a.s_id = c.s_id 
	AND c.c_id = '02' 
WHERE
	b.s_score > c.s_score
```

###### -- 方案二

```sql
SELECT
	a.*,
	b.s_score 01 _score,
	c.s_score 02 _score 
FROM
	student a
	LEFT JOIN score b ON a.s_id = b.s_id 
	AND b.c_id = 01
	LEFT JOIN score c ON a.s_id = c.s_id 
	AND c.c_id = 02 
WHERE
	b.s_score > c.s_score;
```

&nbsp;

### 2、查询"01"课程比"02"课程成绩低的学生的信息及课程分数

```sql
SELECT
	a.*,
	b.s_score 01 _score,
	c.s_score 02 _score 
FROM
	student a
	LEFT JOIN score b ON a.s_id = b.s_id 
	AND b.c_id = 01
	LEFT JOIN score c ON a.s_id = c.s_id 
	AND c.c_id = 02 
WHERE
	b.s_score < c.s_score;
```

&nbsp;

### 3、查询平均成绩大于等于60分的同学的学生编号和学生姓名和平均成绩

```sql
SELECT
	ROUND( avg( a.s_score ) ) avg,
	b.s_id,
	b.s_name 
FROM
	score a
	LEFT JOIN student b ON a.s_id = b.s_id 
GROUP BY
	s_id 
HAVING
	AVG( a.s_score ) >= 60;
```

&nbsp;

### 4、查询平均成绩小于60分的同学的学生编号和学生姓名和平均成绩

​	-- (包括有成绩的和无成绩的)

###### -- 方案一

```sql
SELECT
	S.s_id,
	S.s_name,
	round( avg( ifnull( C.s_score, 0 ) ), 2 ) AS avg_score 
FROM
	Student S
	LEFT JOIN Score C ON S.s_id = C.s_id 
GROUP BY
	s_id 
HAVING
	avg_score < 60;
```

###### -- 方案二

```sql
SELECT
	b.s_id,
	b.s_name,
	ROUND( AVG( a.s_score ), 2 ) AS avg_score 
FROM
	student b
	LEFT JOIN score a ON b.s_id = a.s_id 
GROUP BY
	b.s_id,
	b.s_name 
HAVING
	avg_score < 60 UNION
SELECT
	a.s_id,
	a.s_name,
	0 AS avg_score 
FROM
	student a 
WHERE
	a.s_id NOT IN ( SELECT DISTINCT s_id FROM score );
```

&nbsp;

### 5、查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩

```sql
SELECT
	a.s_id,
	a.s_name,
	count( b.c_id ) AS sum_course,
	sum( b.s_score ) AS sum_score 
FROM
	student a
	LEFT JOIN score b ON a.s_id = b.s_id 
GROUP BY
	a.s_id;
```

&nbsp;

### 6、查询"李"姓老师的数量 

```sql
SELECT
	count( t_id ) 
FROM
	teacher 
WHERE
	t_name LIKE '李%';
```

&nbsp;

### 7、查询学过"张三"老师授课的同学的信息 

```sql
SELECT
	d.* 
FROM
	student d,
	(
	SELECT
		c.s_id 
	FROM
		score c 
	WHERE
		c.c_id IN ( SELECT b.c_id FROM teacher a, course b WHERE a.t_id = b.t_id AND a.t_name = '张三' ) 
	) e 
WHERE
	d.s_id = e.s_id
```

&nbsp;

### 8、查询没学过"张三"老师授课的同学的信息 

```sql
SELECT
	* 
FROM
	student 
WHERE
	s_id NOT IN (
		(
		SELECT
			c.s_id 
		FROM
			score c 
		WHERE
			c.c_id IN ( SELECT b.c_id FROM teacher a, course b WHERE a.t_id = b.t_id AND a.t_name = '张三' ) 
		) 
	)
```

&nbsp;

### 9、查询学过编号为"01"并且也学过编号为"02"的课程的同学的信息

```sql
SELECT
	c.* 
FROM
	score a,
	score b,
	student c 
WHERE
	a.c_id = 01 
	AND b.c_id = 02
	AND a.s_id = b.s_id 
	AND c.s_id = a.s_id
```

&nbsp;

### 10、查询学过编号为"01"但是没有学过编号为"02"的课程的同学的信息

```sql
SELECT
	* 
FROM
	student a 
WHERE
	a.s_id NOT IN ( SELECT DISTINCT s_id FROM score WHERE c_id = 02 ) 
	AND a.s_id IN ( SELECT DISTINCT s_id FROM score WHERE c_id = 01 )
```



