### MyISAM和InnoDB对比

![image-20220413210528200](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220413210528200.png)



### 七种JOIN及SQL语句

1. #### left join

   <img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416190652686.png" alt="image-20220416190652686" style="zoom:50%;" />

   ~~~mysql
   Select * from t_A A left join t_B B on A.key=B.key;
   ~~~

   示例：

   ![image-20220416191010400](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416191010400.png)

   ~~~mysql
   select * from t_test t1 left join t_dept t2 on t1.user_id=t2.dept_id ;
   ~~~

   

2. #### right join

   <img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416191253720.png" alt="image-20220416191253720" style="zoom:50%;" />	

   ~~~mysql
   select * from t_A A right join t_B B on A.key = B.key;
   ~~~

   

   示例：

   ![image-20220416191417852](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416191417852.png)

   ~~~mysql
   select * from t_test t1 right join t_dept t2 on t1.user_id=t2.dept_id ;
   ~~~

   

3. #### inner join

   ​	<img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416191524740.png" alt="image-20220416191524740" style="zoom:50%;" />

   

   ~~~mysql
   select * from t_A A inner join t_B B on A.key=B.key;
   ~~~

   示例：

   ![image-20220416191900516](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416191900516.png)

   ~~~mysql
   select * from t_test t1 inner join t_dept t2 on t1.user_id=t2.dept_id;
   ~~~

   

4. #### left join union right join

   <img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416192028297.png" alt="image-20220416192028297" style="zoom:50%;" />

   ~~~mysql
   select * from t_A A left join t_B B on A.key=B.key
   union
   select * from t_A A right join t_B B on A.key = B.key;
   ~~~

   示例：

   ![image-20220416191707403](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416191707403.png)

   ~~~mysql
   select * from t_test t1 left join t_dept t2 on t1.user_id=t2.dept_id
   UNION
   select * from t_test t1 right join t_dept t2 on t1.user_id=t2.dept_id ;
   ~~~

   ​	

   

5. #### 

<img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416193000999.png" alt="image-20220416193000999" style="zoom:50%;" />

~~~mysql
select * from t_A A right join t_B B on A.key=B.key where B.key is null;
~~~

示例：

![image-20220416193403236](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416193403236.png)

~~~mysql
select * from t_test t1 left join t_dept t2 on t1.user_id=t2.dept_id where t2.dept_id is null;
~~~

​			



6. 

   ​	<img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416193038780.png" alt="image-20220416193038780" style="zoom:50%;" />

   ​	

   ~~~mysql
   select * from t_A A right join t_B B on A.key=B.key where A.key is null;
   ~~~

   示例：

   ![image-20220416193218139](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416193218139.png)

   ~~~mysql
   select * from t_test t1 right join t_dept t2 on t1.user_id=t2.dept_id where t1.user_id is null;
   ~~~

   

   

7. 



<img src="C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416192305452.png" alt="image-20220416192305452" style="zoom:50%;" />

~~~mysql
select * from t_A A right join t_B B on A.key=B.key where B.key is null
union
select * from t_A A right join t_B B on A.key=B.key where A.key is null;
~~~

示例：

![image-20220416193524944](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220416193524944.png)

~~~mysql
select * from t_test t1 left join t_dept t2 on t1.user_id=t2.dept_id where t2.dept_id is null
UNION
select * from t_test t1 right join t_dept t2 on t1.user_id=t2.dept_id where t1.user_id is null;
~~~



### 索引

![image-20220425194702034](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220425194702034.png)

#### MySQL 索引失效的情况

1. 不满足最左前缀法则；

2. 使用OR关键字时，OR两侧，一侧时索引，另一侧不是索引。改为两侧索引即可。

3. 使用范围查询，如>，<

4. 使用了函数进行运算再查询。

   ~~~mysql
   explain select * from t_index_test where substring(col1,2,1)='1';
   ~~~

5.  字符串不加单引号，索引会失效。

6.  模糊查询时，第一个字符为%，索引失效。

7. 数据分布影响是否使用索引(例如某列null较少，则where xxx is null则自动选择全表扫描，where xxx is not null 则走索引；null较多，则where is not null 选择全表扫描，where xxx is null 则走索引)

8. use index(index名)建议使用index;

9. ignore index(index名) 忽略某Index

10. force index(index名) 强制使用index



事务隔离级别：

![image-20220426004811961](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220426004811961.png)

为什么索引

![image-20220427205931548](C:\Users\86152\AppData\Roaming\Typora\typora-user-images\image-20220427205931548.png)