## 1、表结构分析

- 共有四张表，学生表Student、课程表Course、教师表Teacher和成绩表SC。
- 学生表Student：
  -  SId：学生编号。
  - Sname：学生姓名。
  - Sage：出生年月。
  - Ssex：学生性别。
- 课程表Course：
  - CId：课程编号。
  - Cname：课程名称。
  - TId：教师编号。
- 教师表Teacher：
  - TId：教师编号。
  - Tname：教师姓名。
- 成绩表SC：
  - SId：学生编号。
  - CId：课程编号。
  - score：课程分数。
- 表之间的关系：
  - 学生表和成绩表之间，通过学生编号联系。
  - 课程表和教师表之间，通过教师编号联系。
  - 课程表与成绩表之间，通过课程编号联系。





## 2、1~5题

- 第1题：

  - 题目：

    - 查询” 01 “课程比” 02 “课程成绩高的学生的信息及课程分数。
    - 查询同时存在” 01 “课程和” 02 “课程的情况。
    - 查询存在” 01 “课程但可能不存在” 02 “课程的情况(不存在时显示为 null )。
    -  查询不存在” 01 “课程但存在” 02 “课程的情况。

  - 用到的表：学生表、成绩表。

  - 查询” 01 “课程比” 02 “课程成绩高的学生的信息及课程分数：

    - 因为需要同时显示学生信息和两门课程的分数，因此需要用两次内连接，将两门课程分数的列添加。
    - 两次内连接的连接条件都是学生编号，第一次连接01课程，第二次连接02课程。
    - 最后的筛选条件是01课程比02课程乘积高。

    ```sql
    SELECT a.*,b.`score` '01_score',c.`score` '02_score'
    FROM Student a
    INNER JOIN sc b
    ON a.`SId`=b.`SId` AND b.`CId`='01'
    
    INNER JOIN sc c
    ON a.`SId`=c.`SId` AND c.`CId`='02'
    
    WHERE b.`score` > c.`score`;
    ```

  - 查询同时存在” 01 “课程和” 02 “课程的情况:

    - 其实就相当于上题中不加筛选条件。

    ```sql
    SELECT a.*,b.`score` '01_score',c.`score` '02_score'
    FROM Student a
    INNER JOIN sc b
    ON a.`SId`=b.`SId` AND b.`CId`='01'
    
    INNER JOIN sc c
    ON a.`SId`=c.`SId` AND c.`CId`='02';
    ```

  - 查询存在” 01 “课程但可能不存在” 02 “课程的情况(不存在时显示为 null )：

    - 02课程不存在时显示为null，也就是将第二次的内连接改为左外连接。

    ```sql
    SELECT a.*,b.`score` '01_score',c.`score` '02_score'
    FROM Student a
    INNER JOIN sc b
    ON a.`SId`=b.`SId` AND b.`CId`='01'
    
    LEFT OUTER JOIN sc c
    ON a.`SId`=c.`SId` AND c.`CId`='02';
    ```

  -  查询不存在” 01 “课程但存在” 02 “课程的情况:

    - 与上题类似，但是注意要先做内连接。

    ```sql
    SELECT a.*,c.`score` '01_score',b.`score` '02_score'
    FROM Student a
    INNER JOIN sc b
    ON a.`SId`=b.`SId` AND b.`CId`='02'
    
    LEFT OUTER JOIN sc c
    ON a.`SId`=c.`SId` AND c.`CId`='01';
    ```



- 第2题：

  -  查询平均成绩大于等于 60 分的同学的学生编号和学生姓名和平均成绩。
  - 用到的表：学生表、成绩表。
  - 首先，用子查询查询出学生编号和其对应的平均成绩，并进行筛选大于60。
  - 用子查询的结果与学生表进行内连接。

  ```sql
  SELECT a.`SId`,a.`Sname`,c.avg_sc
  FROM Student a
  INNER JOIN(
  SELECT AVG(b.`score`) avg_sc,b.`SId` SId
  FROM sc b
  GROUP BY b.`SId`
  HAVING avg_sc>60
  ) c ON a.`SId`=c.SId;
  ```



- 第3题：

  - 查询在 SC 表存在成绩的学生信息。
  - 用到的表：学生表、成绩表。
  - 将学生表和成绩表进行内连接，然后只显示学生信息并去重。

  ```sql
  SELECT DISTINCT a.*
  FROM Student a
  INNER JOIN sc b
  ON a.`SId`=b.`SId`;
  ```



- 第4题：

  - 查询所有同学的学生编号、学生姓名、选课总数、所有课程的总成绩(没成绩的显示为 null )。
  - 先用子查询，分别查出学生编号对应的选课总数和课程总成绩。
  - 因为没有成绩的显示为null，所以用左外连接。
  - 先左外连接，再分组也可以。

  ```sql
  SELECT a.SId,a.Sname,b.c_count,b.c_sum
  FROM Student a
  LEFT OUTER JOIN (
  	SELECT COUNT(*) c_count,SUM(score) c_sum,SId
  	FROM sc
  	GROUP BY SId
  ) b ON a.SId=b.SId;
  --------------------------------------------
  SELECT a.SId,a.Sname,COUNT(*) c_count,SUM(score) c_sum
  FROM Student a
  LEFT OUTER JOIN sc b
  ON a.SId=b.SId
  GROUP BY SId;
  ```

  

- 第5题：

  - 查询「李」姓老师的数量 。
  - 用到的表：教师表。

  ```sql
  SELECT COUNT(*)
  FROM teacher a
  WHERE a.`Tname` LIKE "李%";
  ```

  



## 3、6~10题

- 第6题：

  - 查询学过「张三」老师授课的同学的信息 。
  - 用到的表：四张表都要用到。教师表必须由课程表关联，课程表必须由成绩表才能关联到学生表。
  - 学生表用SId关联成绩表，成绩表用CId关联课程表，课程表用TId关联教师表。三次内连接。

  ```sql
  SELECT a.*
  FROM student a
  INNER JOIN sc b
  ON a.`SId`=b.`SId`
  
  INNER JOIN course c
  ON b.`CId`=c.`CId`
  
  INNER JOIN teacher d
  ON c.`TId`=d.`TId`
  
  WHERE d.`Tname`='张三';
  ```

- 第7题：

  - 查询没有学全所有课程的同学的信息 。
  - 用到的表：学生表，成绩表。
  - 首先查出所有课程的同学的SId，然后以SId不在这些结果中进行筛选。

  ```sql
  SELECT a.*
  FROM student a
  WHERE  a.`SId` NOT IN(
  	SELECT b.`SId`
  	FROM sc b
  	GROUP BY b.SId
  	HAVING COUNT(*)=(
  		SELECT COUNT(*)
  		FROM course
  	)
  );
  ```

- 第8题：

  - 查询至少有一门课与学号为” 01 “的同学所学相同的同学的信息 。
  - 用到的表：学生表，成绩表。
  - 先查出学号为01的同学所学的课程。
  - 然后将学生表和成绩表内连接，筛选条件为课程CId在01同学所学的课程中。
  - 最后用distinct对学生信息去重。

  ```sql
  SELECT DISTINCT a.*
  FROM student a
  INNER JOIN sc b
  ON a.`SId`=b.`SId`
  WHERE b.`CId` IN (
  	SELECT d.`CId`
  	FROM student c
  	INNER JOIN sc d
  	ON c.`SId`=d.`SId`
  	WHERE c.`SId`='01' 
  );
  ```

- 第9题：

  - 查询和” 01 “号的同学学习的课程完全相同的其他同学的信息 。
  - 用到的表：学生表，成绩表。
  - 在上一题的基础上，先不去重，则剩下的都是选的课在01选的课之中的信息。那么如果剩下的每个同学的信息与01选的课的数量相同，则选的课是完全相同。

  ```sql
  SELECT a.*
  FROM student a
  INNER JOIN sc b
  ON a.`SId`=b.`SId`
  WHERE b.`CId` IN (
  	SELECT d.`CId`
  	FROM student c
  	INNER JOIN sc d
  	ON c.`SId`=d.`SId`
  	WHERE c.`SId`='01' 
  ) GROUP BY a.`SId`
  HAVING COUNT(*)=(
  	SELECT COUNT(*)
  	FROM sc e
  	WHERE e.`SId`='01'
  );
  ```

- 第10题：

  - 查询没学过”张三”老师讲授的任一门课程的学生姓名 。
  - 用到的表：四张表都要用到。
  - 首先查出张三讲授的课程编号，看作一列。
  - 将学生表和成绩表内联，然后左外连接张三讲授的课程编号。
  - 最后按学生编号分支，计算左外连接到的课程编号中不为null的个数。个数为0的筛选出来。

  ```sql
  SELECT a.`Sname`
  FROM student a
  INNER JOIN sc b
  ON a.`SId`=b.`SId`
  
  LEFT OUTER JOIN (
  	SELECT d.`CId` c_cid
  	FROM teacher c
  	INNER JOIN course d
  	ON c.`TId`=d.`TId`
  	WHERE c.`Tname`='张三'
  ) e
  ON b.`CId`=e.c_cid
  GROUP BY a.`SId`
  HAVING COUNT(e.c_cid)=0;
  ```

  

## 4、11~15题

- 第11题：

  - 查询两门及其以上不及格课程的同学的学号，姓名及其平均成绩 。
  - 用到的表：学生表，课程表。
  - 首先子查询查出两门及其以上不及格的学号。
  - 学生表内连接子查询，并且计算出平均分。

  ```sql
  SELECT b.`SId`,b.`Sname`,(
  	SELECT AVG(d.score)
  	FROM sc d
  	WHERE c.a_sid=d.SId
  	GROUP BY d.SId
  ) sc_avg
  FROM student b
  INNER JOIN (
  	SELECT a.`SId` a_sid
  	FROM sc a
  	WHERE a.`score`<60
  	GROUP BY a.`SId`
  	HAVING COUNT(*)>=2
  ) c ON b.`SId`=c.a_sid;
  ```

- 第12题：

  - 检索” 01 “课程分数小于 60，按分数降序排列的学生信息。
  - 用到的表：学生表，成绩表。
  - 查出01课程分数小于60的学生编号，然后与学生表内连接，最后排序。

  ```sql
  SELECT a.*,c.score a_score
  FROM student a
  INNER JOIN (
  	SELECT b.`score`,b.`SId`
  	FROM sc b
  	WHERE b.`CId`='01' AND b.`score`<60
  ) c ON a.`SId`=c.SId
  ORDER BY a_score DESC;
  ```

- 第13题：

  - 按平均成绩从高到低显示所有学生的所有课程的成绩以及平均成绩。
  - 用到的表：学生表，成绩表。
  - 先计算出所有学生的平均分，然后与成绩表自连接，最后排序。

  ```sql
  SELECT	b.*,c.c_avg
  FROM sc b
  INNER JOIN (
  	SELECT AVG(a.`score`) c_avg,a.`SId`
  	FROM sc a
  	GROUP BY a.`SId`
  ) c ON b.`SId`=c.SId
  ORDER BY c.c_avg DESC;
  ```

- 第14题：

  - 查询各科成绩最高分、最低分和平均分：
    - 以如下形式显示：课程 ID，课程 name，最高分，最低分，平均分，及格率，中等率，优良率，优秀率
    - 及格为>=60，中等为：70-80，优良为：80-90，优秀为：>=90
    - 要求输出课程号和选修人数，查询结果按人数降序排列，若人数相同，按课程号升序排列
  - 用到的表：课程表，学生表，成绩表。
  - 多个子查询左外连接。注意为例避免null出现，需要用ifnull函数，尤其是计算count个数时。

  ```sql
  SELECT a.`CId` 课程ID,a.`Cname` 课程name,IFNULL(b.b_max,-1) 最高分,IFNULL(c.c_min,-1) 最低分,
  	IFNULL(d_avg,-1) 平均分,IFNULL(e_count,0) 选修人数,
  	IFNULL(count_you,0)/IFNULL(e_count,1) 优秀率,
  	IFNULL(count_liang,0)/IFNULL(e_count,1) 良好率,
  	IFNULL(count_zhong,0)/IFNULL(e_count,1) 中等率,
  	IFNULL(count_jige,0)/IFNULL(e_count,1) 及格率
  FROM course a
  LEFT JOIN (
  	SELECT CId,MAX(score) b_max
  	FROM sc
  	GROUP BY CId
  ) b ON a.`CId`=b.CId
  
  LEFT JOIN (
  	SELECT CId,MIN(score) c_min
  	FROM sc
  	GROUP BY CId
  ) c ON a.`CId`=c.CId
  
  LEFT JOIN (
  	SELECT CId,AVG(score) d_avg
  	FROM sc
  	GROUP BY CId
  ) d ON a.`CId`=d.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) e_count
  	FROM sc
  	GROUP BY CId
  ) e ON a.`CId`=e.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_you
  	FROM sc 
  	WHERE score>=90
  	GROUP BY CId
  ) f ON a.`CId`=f.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_liang
  	FROM sc
  	WHERE score<90 AND score>=80
  	GROUP BY CId
  ) g ON a.`CId`=g.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_zhong
  	FROM sc
  	WHERE score<80 AND score>=70
  	GROUP BY CId
  ) h ON a.`CId`=h.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_jige
  	FROM sc
  	WHERE score>=60
  	GROUP BY CId
  ) i ON a.`CId`=i.CId
  ORDER BY e_count,a.`CId`;
  ```

- 第15题：

  - 按各科成绩进行排序，并显示排名， Score 重复时保留名次空缺。
  - 用到的表：成绩表。
  - 在select后子查询，名次其实就是在当前科目中，分数比自己低的人数加1。需要注意使用count函数，没有比他小的时候是null。
  - 分数重复就看的是count的是比自己低的人数还是分数数，如果合并需要加distinct。

  ```sql
  SELECT a.`CId`,IFNULL((
  	SELECT COUNT(score)+1
  	FROM sc b
  	WHERE a.`CId`=b.CId AND b.score>a.`score`
  	GROUP BY b.CId
  ),1) number,a.`SId`,a.`score`
  FROM sc a
  ORDER BY CId,number;
  
  ```

  - 按各科成绩进行排序，并显示排名， Score 重复时合并名次。

  ```sql
  SELECT a.`CId`,IFNULL((
  	SELECT COUNT(DISTINCT score)+1
  	FROM sc b
  	WHERE a.`CId`=b.CId AND b.score>a.`score`
  	GROUP BY b.CId
  ),1) number,a.`SId`,a.`score`
  FROM sc a
  ORDER BY CId,number;
  ```



## 5、16~20题

- 第16题：

  - 查询学生的总成绩，并进行排名，总分重复时保留名次空缺。
  - 用到的表：成绩表。
  - 与15题类似，只不过这次使用的子表不是成绩表，而是子查询产生的总成绩表。

  ```sql
  SELECT b.*,IFNULL((
  	SELECT COUNT(sum_score)+1
  	FROM (
  		SELECT a.`SId`,SUM(score) sum_score
  		FROM sc a
  		GROUP BY a.`SId`
  	) c
  	WHERE c.sum_score>b.sum_score
  ),1) number
  FROM (
  	SELECT a.`SId`,SUM(score) sum_score
  	FROM sc a
  	GROUP BY a.`SId`
  )b ORDER BY number;
  ```

  - 查询学生的总成绩，并进行排名，总分重复时不保留名次空缺。

  ```sql
  SELECT b.*,IFNULL((
  	SELECT COUNT(DISTINCT sum_score)+1
  	FROM (
  		SELECT a.`SId`,SUM(score) sum_score
  		FROM sc a
  		GROUP BY a.`SId`
  	) c
  	WHERE c.sum_score>b.sum_score
  ),1) number
  FROM (
  	SELECT a.`SId`,SUM(score) sum_score
  	FROM sc a
  	GROUP BY a.`SId`
  )b ORDER BY number;
  ```

- 17题：

  - 统计各科成绩各分数段人数：课程编号，课程名称，[100-85]，[85-70]，[70-60]，[60-0] 及所占百分比。
  - 用到的表：课程表，成绩表。
  - 类似14题。

  ```sql
  SELECT a.`CId` 课程ID,a.`Cname` 课程name,
  	IFNULL(count_you,0) `[100-85]`,
  	IFNULL(count_you,0)/IFNULL(e_count,1) `[100-85]%`,
  	IFNULL(count_liang,0) `[85-70]%`,
  	IFNULL(count_liang,0)/IFNULL(e_count,1) `[85-70]`,
  	IFNULL(count_zhong,0) `[70-60]`,
  	IFNULL(count_zhong,0)/IFNULL(e_count,1) `[70-60]%`,
  	IFNULL(count_jige,0) `[60-0]`,
  	IFNULL(count_jige,0)/IFNULL(e_count,1) `[60-0]%`
  FROM course a
  LEFT JOIN (
  	SELECT CId,COUNT(score) e_count
  	FROM sc
  	GROUP BY CId
  ) e ON a.`CId`=e.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_you
  	FROM sc
  	WHERE score<=100 AND score>=85
  	GROUP BY CId
  ) f ON a.`CId`=f.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_liang
  	FROM sc
  	WHERE score<85 AND score>=70
  	GROUP BY CId
  ) g ON a.`CId`=g.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_zhong
  	FROM sc
  	WHERE score<70 AND score>=60
  	GROUP BY CId
  ) h ON a.`CId`=h.CId
  
  LEFT JOIN (
  	SELECT CId,COUNT(score) count_jige
  	FROM sc
  	WHERE score<60 AND score>=0
  	GROUP BY CId
  ) i ON a.`CId`=i.CId
  ORDER BY e_count,a.`CId`;
  ```

- 18题：

  - 查询各科成绩前三名的记录。
  - 用到的表：成绩表，课程表。
  - 将前三名转化为低于此分数的人少于3个就是前三名，至于分数相同导致多于3人暂不处理。

  ```sql
  SELECT a.`CId`,a.`SId`,a.`score`
  FROM sc a
  WHERE (
  	SELECT COUNT(*)
  	FROM sc b
  	WHERE a.`CId`=b.`CId` AND a.`score`<b.`score`
  )<3
  ORDER BY CId,score DESC;
  ```

- 第19题：

  - 查询每门课程被选修的学生数 。
  - 用到的表：课程表，成绩表。

  ```sql
  SELECT b.`Cname`,c.c_count
  FROM course b
  INNER JOIN (
  	SELECT a.`CId`,COUNT(*) c_count
  	FROM sc a
  	GROUP BY a.`CId`
  ) c ON b.`CId`=c.CId;
  ```

- 第20题：

  - 查询出只选修两门课程的学生学号和姓名 。
  - 用到的表：学生表，成绩表。
  - 先子查询出每个同学选修的课程数。

  ```sql
  SELECT b.`SId`,b.`Sname`
  FROM student b
  INNER JOIN (
  	SELECT a.`SId` c_cid,COUNT(*) c_count
  	FROM sc a
  	GROUP BY a.`SId`
  ) c ON b.`SId`=c.c_cid
  WHERE c.c_count=2;
  ```

  

## 6、21~25题

- 第21题：

  - 查询男生、女生人数。
  - 用到的表：学生表。

  ```sql
  SELECT	Ssex,COUNT(*)
  FROM student
  GROUP BY Ssex;
  ```

- 第22题：

  - 查询名字中含有「风」字的学生信息。
  - 用到的表：学生表。

  ```sql
  SELECT *
  FROM student
  WHERE Sname LIKE "%风%";
  ```

- 第23题：

  - 查询同名同性学生名单，并统计同名人数。
  - 用到的表：学生表。
  - 即按姓名性别分组，组中个数大于1的即为有重名的。

  ```sql
  SELECT	*,COUNT(*) s_count
  FROM student
  GROUP BY Sname,Ssex
  HAVING s_count>1;
  ```

- 第24题：

  - 查询 1990 年出生的学生名单。
  - 用到的表：学生表。

  ```sql
  SELECT *
  FROM student
  WHERE YEAR(Sage)=1990;
  ```

- 第25题：

  - 查询每门课程的平均成绩，结果按平均成绩降序排列，平均成绩相同时，按课程编号升序排列。
  - 用到的表：成绩表。

  ```sql
  SELECT CId,AVG(score) sc_avg
  FROM sc
  GROUP BY CId
  ORDER BY sc_avg DESC,CId;
  ```

  

## 7、26~30题

- 第26题：

  - 查询平均成绩大于等于 85 的所有学生的学号、姓名和平均成绩 。
  - 用到的表：学生表，成绩表。
  - 先用子查询查出学生的平均成绩。

  ```sql
  SELECT a.`SId`,a.`Sname`,b.sc_avg
  FROM student a
  INNER JOIN (
  	SELECT SId,AVG(score) sc_avg
  	FROM sc
  	GROUP BY SId
  ) b
  ON a.`SId`=b.`SId`
  WHERE b.sc_avg>85;
  ```

- 第27题：

  - 查询课程名称为「数学」，且分数低于 60 的学生姓名和分数 。
  - 用到的表：课程表，学生表，成绩表。
  - 先查出数学课程的成绩。

  ```sql
  SELECT c.`Sname`,d.score
  FROM student c
  INNER JOIN (
  	SELECT a.`SId`,a.`score`
  	FROM sc a
  	INNER JOIN course b
  	ON a.`CId`=b.`CId`
  	WHERE b.`Cname`="数学"
  ) d ON c.`SId`=d.SId
  WHERE d.score<60;
  ```

- 第28题：

  - 查询所有学生的课程及分数情况（存在学生没成绩，没选课的情况）。
  - 用到的表：学生表，课程表，成绩表。

  ```sql
  SELECT a.`Sname`,d.Cname,d.score
  FROM student a
  INNER JOIN (
  	SELECT b.`SId`,c.`Cname`,b.`score`
  	FROM sc b
  	INNER JOIN course c
  	ON b.`CId`=c.`CId`
  ) d ON a.`SId`=d.SId;
  ```

- 第29题：

  - 查询任何一门课程成绩在 70 分以上的姓名、课程名称和分数。
  - 用到的表：成绩表，学生表，课程表。
  - 两次内连接，第一次连接成绩表和课程表，筛选70分以上后，再与学生表连接。

  ```sql
  SELECT c.`Sname`,d.Cname,d.score
  FROM student c
  INNER JOIN (
  	SELECT a.`CId`,a.`Cname`,b.score,b.SId
  	FROM course a
  	INNER JOIN (
  		SELECT SId,CId,score
  		FROM sc
  		WHERE score>70
  	) b ON a.`CId`=b.CId
  	WHERE b.score>70
  ) d ON c.`SId`=d.SId;
  ```

- 第30题：

  - 查询不及格的课程。
  - 用到的表：成绩表，学生表，课程表。

  ```sql
  SELECT b.`Cname`,d.`Sname`,c.score
  FROM course b
  INNER JOIN (
  	SELECT CId,SId,score
  	FROM sc 
  	WHERE score<60
  ) c ON b.`CId`=c.CId
  INNER JOIN student d
  ON c.SId=d.`SId`;
  ```

  

## 8、31~35题

- 第31题：

  - 查询课程编号为 01 且课程成绩在 80 分以上的学生的学号和姓名。
  - 用到的表：学生表，成绩表。

  ```sql
  SELECT a.`SId`,a.`Sname`,b.score
  FROM student a
  INNER JOIN (
  	SELECT SId,score
  	FROM sc
  	WHERE CId=01 AND score>80
  ) b ON a.`SId`=b.SId;
  ```

- 第32题：

  - 求每门课程的学生人数 。
  - 用到的表：成绩表，课程表。

  ```sql
  SELECT a.`Cname`,b.s_count
  FROM course a
  INNER JOIN (
  	SELECT CId,COUNT(*) s_count
  	FROM sc
  	GROUP BY CId
  ) b ON a.`CId`=b.CId;
  ```

- 第33题：

  - 成绩不重复，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩。
  - 用到的表：四张表都要用到。
  - 先子查询和内连接查出张三所教学生及其成绩，然后用排序取第一个的方式获得最高成绩。

  ```sql
  SELECT f.*,e.score
  FROM (
  	SELECT d.`SId`,d.`score`
  	FROM (
  		SELECT a.`CId`
  		FROM course a
  		INNER JOIN teacher b
  		ON a.`TId`=b.`TId`
  		WHERE b.`Tname`="张三"
  	) c INNER JOIN sc d
  	ON c.CId=d.`CId`
  	ORDER BY d.`score`DESC
  	LIMIT 1
  ) e INNER JOIN student f
  ON e.SId=f.`SId`;
  ```

- 第34题：

  - 成绩有重复的情况下，查询选修「张三」老师所授课程的学生中，成绩最高的学生信息及其成绩。
  -  用到的表：四张表都要用到。
  - 就是将之前的选取最高成绩的方式改为，分数等于该科目的最高分。

  ```sql
  SELECT f.*,e.score
  FROM (
  	SELECT d.`SId`,d.`score`
  	FROM (
  		SELECT a.`CId`
  		FROM course a
  		INNER JOIN teacher b
  		ON a.`TId`=b.`TId`
  		WHERE b.`Tname`="张三"
  	) c INNER JOIN sc d
  	ON c.CId=d.`CId`
  	INNER JOIN (
  		SELECT CId,MAX(score) sc_max
  		FROM sc
  		GROUP BY CId
  	) g ON c.CId=g.CId
  	WHERE d.`score`=g.sc_max
  ) e INNER JOIN student f
  ON e.SId=f.`SId`;
  ```

- 第35题：

  - 查询不同课程成绩相同的学生的学生编号、课程编号、学生成绩 。
  - 用到的表：成绩表，学生表。

  ```sql
  SELECT a.SId
  FROM (	
  	SELECT a.SId,COUNT(*) a_count
  	FROM (
  		SELECT DISTINCT SId,score
  		FROM sc
  	) a GROUP BY a.SId
  ) a
  INNER JOIN (
  	SELECT SId,COUNT(*) b_count
  	FROM sc
  	GROUP BY SId
  ) b ON a.SId=b.SId
  WHERE a.a_count!=b.b_count;
  ```

  

## 9、36~40题

- 第36题：

  - 查询每门课程成绩最好的前两名。
  - 用到的表：成绩表。
  - 前两名即比自己分高的少于两人。

  ```sql
  SELECT a.`SId`,c.`CId`,a.`score`
  FROM sc a
  INNER JOIN course c
  ON a.`CId`=c.CId
  INNER JOIN student d
  ON a.`SId`=d.SId
  WHERE (
  	SELECT COUNT(*)
  	FROM sc b
  	WHERE a.`CId`=b.`CId` AND a.`score`<b.`score`
  ) < 2 
  ORDER BY a.`CId`,a.`score` DESC;
  ```

- 第37题：

  - 统计每门课程的学生选修人数（超过 5 人的课程才统计）。
  - 用到的表：成绩表。

  ```sql
  SELECT b.`Cname`,COUNT(*) c_count
  FROM sc a
  INNER JOIN course b
  ON a.`CId`=b.`CId`
  INNER JOIN student c
  ON a.`SId`=c.`SId`
  GROUP BY a.CId
  HAVING c_count>5;
  ```

- 第38题：

  - 检索至少选修两门课程的学生学号 。
  - 用到的表：学生表，成绩表。

  ```sql
  SELECT a.`SId`,COUNT(*) c_count
  FROM sc a
  GROUP BY a.`SId`
  HAVING c_count>=2;
  ```

- 第39题：

  - 查询选修了全部课程的学生信息。
  - 用到的表：学生表，成绩表，课程表。
  - 选修了所有课程就是选修课程数等于课程表中的课程数。

  ```sql
  SELECT c.*
  FROM sc a
  INNER JOIN student c
  ON a.`SId`=c.`SId`
  GROUP BY a.`SId`
  HAVING COUNT(*) = (
  	SELECT COUNT(*) c_count
  	FROM course
  );
  ```

- 第40题：

  - 查询各学生的年龄，只按年份来算 。
  - 用到的表：学生表。

  ```sql
  SELECT Sname,YEAR(NOW())-YEAR(Sage) age
  FROM student;
  ```

  

## 41~45题

- 第41题：

  - 按照出生日期来算，当前月日 < 出生年月的月日则，年龄减一。
  - 用到的表：学生表。
  - 使用`timestampdiff(year,小日期,大日期)`。

  ```sql
  SELECT Sname,TIMESTAMPDIFF(YEAR,Sage,NOW()) age
  FROM student;
  ```

- 第42题：

  - 查询本周过生日的学生。
  - 用到的表：学生表。

  ```sql
  SELECT *
  FROM student
  WHERE WEEK(Sage)=WEEK(NOW());
  
  SELECT *
  FROM student
  WHERE WEEK(Sage)=WEEK("2020-12-18");
  ```

- 第43题：

  - 查询下周过生日的学生。
  - 用到的表：学生表。
  - 使用`DATE_ADD(Sage,INTERVAL -1 WEEK)`可以获得向前一周的日期。

  ```sql
  SELECT *
  FROM student
  WHERE WEEK(Sage)=WEEK(NOW())+1
  OR WEEK(DATE_ADD(Sage,INTERVAL -1 WEEK))=WEEK(NOW());
  
  SELECT *
  FROM student
  WHERE WEEK(Sage)=WEEK("2020-12-29")+1
  OR WEEK(DATE_ADD(Sage,INTERVAL -1 WEEK))=WEEK("2020-12-29");
  ```

- 第44题：

  - 查询本月过生日的学生。
  - 用到的表：学生表。

  ```sql
  SELECT *
  FROM student
  WHERE MONTH(Sage)=MONTH(NOW());
  
  SELECT *
  FROM student
  WHERE MONTH(Sage)=MONTH("2020-12-18");
  ```

- 第45题：

  - 查询下月过生日的学生。
  - 用到的表：学生表。

  ```sql
  SELECT *
  FROM student
  WHERE MONTH(Sage)=MONTH(NOW())+1
  OR MONTH(DATE_ADD(Sage,INTERVAL -1 MONTH))=MONTH(NOW());
  
  SELECT *
  FROM student
  WHERE WEEK(Sage)=WEEK("2020-12-29")+1
  OR MONTH(DATE_ADD(Sage,INTERVAL -1 MONTH))=MONTH("2020-12-29");
  ```

  

