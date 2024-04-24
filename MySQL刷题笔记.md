#  177第N高薪水

![image-20240321192503299](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240321192503299.png)



```mysql
CREATE FUNCTION getNthHighestSalary(N INT) RETURNS INT
BEGIN
  DeCLARE M INT;
  SET M= N-1;
  RETURN (
     
        select distinct salary 
        from Employee
        order by salary DESC limit M,1
  );
END
```

## LIMIT a  ： 获取前a条数据

## LIMIT a，b ： 获取前a+1，a+2 .... a+b 条数据

## LIMIT a OFFSET b ： 获取b+1，b+2 ...b+a条数据







# 178 分数排名

![image-20240321195101631](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240321195101631.png)

```mysql
select  score,dense_rank() over(ORDER BY score desc)  as "rank"
from Scores
```

## mysql 排名函数：

example：![image-20240321195254086](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240321195254086.png)

```mysql
CREATE TABLE student_score(
id int not null ,
name varchar(20) not null,
score int not null);
INSERT INTO student_score(id,name,score) VALUES(1,'鲁班',45);
INSERT INTO student_score(id,name,score) VALUES(2,'鲁班大师',32);
INSERT INTO student_score(id,name,score) VALUES(3,'李元芳',67);
INSERT INTO student_score(id,name,score) VALUES(4,'西施',67);
INSERT INTO student_score(id,name,score) VALUES(5,'貂蝉',89);
INSERT INTO student_score(id,name,score) VALUES(6,'吕布',92);
INSERT INTO student_score(id,name,score) VALUES(7,'赵云',75);
```



### row_number(  )  

对同一个字段进行排序，即使出现同分数，也不会并列排序，即排序按照1，2，3，4，5……的顺序。

```mysql
SELECT *,row_number() over(ORDER BY score desc) as "row_number" from student_score;
```

![image-20240321195447240](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240321195447240.png<img src="C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240321195627257.png" alt="image-20240321195627257"  />

### rank(   )

对同一个字段进行排序，出现同分数进行并列排序，例如1，2，2，4，……，出现两次第四名并跳过第五名，也可以称作跳跃排名函数。

```mysql
SELECT *,rank() over(ORDER BY score desc) as "rank" from student_score;
```

![image-20240321195727237](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240321195727237.png)

### dense_rank(   )

和rank()函数类似，对同一个字段进行排序，出现同分数进行并列排序，例如1，2，2，3，……，出现两次第二名但不会跳过第三名，也可以称作连续排名函数。

```mysql
SELECT *,dense_rank() over(ORDER BY score desc) as "dense_rank" from student_score; 
```

![image-20240321195953308](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240321195953308.png)

### ntile(  x  )

将数据分成x组

```mysql
SELECT * , ntile(3) over (ORDER BY score DESC) as "ntile" from student_score;
```





# 180连续出现的数字

![image-20240322131517197](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322131517197.png)

```mysql
select distinct l1.num as "ConsecutiveNums"
from logs l1 , logs l2 ,logs l3
where l1.num = l2.num 
    and l2.num = l3.num 
    and l1.id = l2.id -1 
    and l2.id = l3.id -1;
```



# 181超过经理收入的员工

![image-20240322132301733](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322132301733.png)

使用where

```MySQL
select E1.name as "Employee"
from Employee E1,Employee E2
where E1.managerId = E2.id and E1.salary > E2.salary;
```

使用join

```mysql
select E1.name as "Employee"
from Employee E1  join Employee E2
where E1.managerId = E2.id and E1.salary > E2.salary;
```

## JOIN图解：

![image-20240322132703114](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322132703114.png)



# 182查找重复的电子邮件

![image-20240322133042648](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322133042648.png)

```mysql
select distinct email as "Email"
from Person 
group by email
having count(email) > 1
```


# 183从不订购的客户

![image-20240322133450725](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322133450725.png)

## 子查询：

```mysql
select c.name as "Customers"
from Customers c
where c.id not in (
    select distinct customerId
    from Orders
);
```

## 左外连接 LEFT JOIN：

```mysql
select c.name as "Customers"
from Customers c LEFT JOIN Orders o on c.id = o.customerId 
where o.customerId is null
```

## *is null 和 = null* ：

NULL在数据库中不表示任何值，而=NULL属于逻辑比较，所以不管什么值和NULL比较都会显示的false，从而无法选出想要的结果。





# 184部门工资最高的员工

![image-20240322135430317](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322135430317.png)

![image-20240322135441584](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322135441584.png)

```mysql
select d.name as "Department" , e1.name as "Employee" , e1.salary as "Salary"
from Employee e1 LEFT JOIN Department d on e1.departmentId = d.id 
where (e1.salary , e1.departmentId) in (
                                select Max(salary),departmentId
                                from Employee
                                group by departmentId 
                            );
```



# 185 部门工资前三高的所以员工

![image-20240322151430854](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322151430854.png)

```mysql
select d.name as "Department", e1.name as "Employee", e1.salary as "Salary"
from Employee e1
         left join department d on e1.departmentId = d.id
where (
    
    select count(distinct e2.salary)
    from Employee e2
    where e1.salary <= e2.salary  and e1.departmentId = e2.departmentId
          )  <= 3;
```

# 196删除重复的电子邮箱

![image-20240322155158364](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322155158364.png)

## 子查询

```mysql
delete 
from Person
where Person.id not in (
    select temp.id
    from
    (
        select min(id) as "id" from Person group by email
    ) as temp  
);
```

## 自然连接

```mysql
delete p1
from Person p1 , Person p2
where p1.email = p2.email and p1.id > p2.id 
```





# 197上升的温度

![image-20240322161502859](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322161502859.png)

## 相关子查询：

```mysql
select w1.id
from Weather w1
where w1.temperature > (
    select w2.temperature
    from Weather w2
    where w1.recordDate = DATE_ADD(w2.recordDate,INTERVAL 1 day )
    );
```

## 左外连接 LEFT JOIN 

```mysql
select w1.id
from Weather w1 left join Weather w2 on w1.recordDate = DATE_ADD(w2.recordDate,INTERVAL 1 day)
where w1.temperature > w2.temperature;
```

## DATE_ADD 函数：

可以实现日期的 + -

example : 

```mysql
DATE_ADD(@date,INTERVAL 1 day) #在date的基础上增加一天
DATE_ADD(@date,INTERVAL -1 day) #在date的基础上减少一天
# 除了 day 的单位外还有  year quarter month week day hour minute scond microsecond  （按单位大小排序）
```







# 262行程和用户

![image-20240322170625840](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322170625840.png)

![image-20240322170636156](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322170636156.png)

![image-20240322170650349](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240322170650349.png)

```mysql
select request_at as "DAY",
       round(count(IF(status != 'completed', status, NULL)) / count(*), 2) as "Cancellation Rate"
from Trips
where client_id in (select users_id from Users where banned = 'No' and role = 'client')
  and driver_id in (select users_id from Users where banned = 'NO' and role = 'driver')
  and request_at between '2013-10-01' and '2013-10-03'
group by request_at;
```

## IF表达式

> **IF**(**expr1** , **expr2** , **expr3**)
>  **expr1**的值为**TRUE**，则返回值为**expr2**
>  **expr1**的值为**FALSE**，则返回值为**expr3**

```mysql
IF(true,1,0) -> 1
IF(false,1,0) -> 0
```

## IFNULL表达式

> **IFNULL**(**expr1** , **expr2**)
>  判断第一个参数**expr1**是否为**NULL**
>  如果**expr1**不为空，直接返回**expr1**
>  如果**expr1**为空，返回第二个参数**expr2**

```mysql
IFNULL('hhh','not null') -> 'hhh'
IFNULL(NULL,"左边NULL了") -> '左边NULL了'
```

## NULLIF表达式

> **NULLIF**(**expr1** , **expr2**)
>  如果**expr1**和**expr2**的值相等返回**NULL**
>  如果值不相等返回**expr1**的值

```mysql
NULLIF('cfs','cfs') -> NULL
NULLIF('cfs',qeee) -> 'cfs'
```







# 511游戏玩法分析

![image-20240323140554618](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240323140554618.png)

```mysql
select distinct player_id as "player_id",date(min(event_date)) as "first_login"
from Activity
group by player_id;
```



# 550游戏玩法分析Ⅳ

![image-20240323142537282](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240323142537282.png)

```mysql
select  ROUND(count(IF(DATE_ADD(first_Login, INTERVAL 1 day) = a1.event_date,'a',NULL))/count(distinct a1.player_id),2) as "fraction"
from Activity a1
         left join (select player_id, min(event_date) as "first_Login"
                    from Activity
                    group by player_id) as temp
                   on a1.player_id = temp.player_id
```

## Round（num,n）：

​					对num进行四舍五入，保留n位小数



# 575 至少有5名直接下属的经理

![image-20240324150338444](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324150338444.png)

![image-20240324150345270](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324150345270.png)

## LEFT JOIN:

```mysql
select e1.name    
from Employee e1 left join Employee e2 on e1.id = e2.managerId
group by e1.id
having count(*)>=5
```

## 子查询：

```mysql
select name
from Employee
where Employee.id in (
    select e2.managerId
    from Employee e2
    group by e2.managerId
    having count(*)>=5
    )
```





# 577员工奖金

![image-20240324151522155](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324151522155.png)

![image-20240324151536981](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324151536981.png)

```mysql
select Employee.name , Bonus.bonus
from Employee LEFT JOIN Bonus on Employee.empId = Bonus.empId
where Bonus.bonus<1000 or Bonus.bonus is null;
## null不是数字0，不能与1000比较大小，需要bonus is null进行判断

select Employee.name , Bonus.bonus
from Employee LEFT JOIN Bonus on Employee.empId = Bonus.empId
where IFNULL(Bonus.bonus,0)<1000
## 用IFNULL可以将null直接转换成0再与1000比较
```



# 584寻找用户推荐人

![image-20240324152109295](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324152109295.png)

![image-20240324152115522](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324152115522.png)

```mysql
select name
from Customer
where IFNULL(Customer.referee_id,0)!=2;
```





# 582 2016年的投资

![image-20240324214826701](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324214826701.png)

![image-20240324214833497](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240324214833497.png)

```mysql
select round(SUM(i.tiv_2016),2) as tiv_2016
from Insurance i
where tiv_2015 in (select i1.tiv_2015
                   from Insurance i1
                   where i.pid != i1.pid)
  and concat(i.lat,i.lon) not in (select concat(i2.lat,i2.lon)
                             from Insurance i2
                             where i.pid != i2.pid);
```





# 586订单最多的客户

![image-20240325104705766](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240325104705766.png)

![image-20240325104712452](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240325104712452.png)

```mysql
select customer_number
from orders
group by customer_number
order by COUNT(*) desc limit 1
```



# 595大的国家

![image-20240325105250441](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240325105250441.png)

```mysql
select name,population,area
from World
where area>=3000000  or population>=25000000
```



# 596超过5名学生的课

![image-20240325105515178](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240325105515178.png)

```mysql
select class
from courses
group by class
having count(*)>=5
```



# 601体育馆的人流量

![image-20240325112008909](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240325112008909.png)

![image-20240325112017653](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240325112017653.png)

```mysql
select distinct s1.id,s1.visit_date,s1.people
from Stadium s1,
     Stadium s2,
     Stadium s3
where ((s1.id = s2.id - 1 and s1.id = s3.id - 2)
   or (s1.id = s2.id - 1 and s1.id = s3.id + 1)
   or (s1.id = s2.id + 1 and s1.id = s3.id + 2))
    and s1.people >= 100
    and s2.people >= 100
    and s3.people >= 100
ORDER BY s1.visit_date
```



# 602 好友申请 Ⅱ：谁有最多的好友

![image-20240326152832149](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240326152832149.png)

![image-20240326152839397](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240326152839397.png)

```mysql

select requester_id as id, count(*) as num
from (select requester_id
      from RequestAccepted
      union all
      select accepter_id
      from RequestAccepted) as tmp
group by requester_id
order by count(*) desc
limit 1;
```

## UNION and UNION ALL

**UNION**: 连接数据集关键字，可以将两个查询结果集拼接为一个，**会过滤掉相同的记录**

**UNION ALL**:连接数据集关键字，可以将两个查询结果集拼接为一个，**不会过滤掉相同的记录**







# 607销售员



```mysql
select name
from salesperson
where sales_id not in (select distinct sales_id
                       from Orders
                       where com_id = (select com_id from company where name = 'RED'));
```



# 608树节点

```mysql
select id,
       IF(ISNULL(p_id)
           ,'Root'
           , IF( (select count(*) from Tree t2 where t2.p_id = t1.id) > 0,'Inner','Leaf')
        )
       as type
from Tree t1 ;
```



# 610判断三角形

![image-20240327142341737](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240327142341737.png)

![image-20240327142349865](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240327142349865.png)

```mysql
select *, IF(x+y>z and x+z>y and z+y>x,'Yes','No') as triangle
from Triangle;
```



# 619只出现一次的最大数字

![image-20240327144009204](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240327144009204.png)

```mysql
select MAX(num) as "num"
from (select num
      from MyNumbers
      group by num
      having count(*) = 1) tmp
```

tips:MAX(NULL) -> NULL





# 620有趣的电影

![image-20240327144249379](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240327144249379.png)

```mysql
select *
from cinema
where id%2=1 and description!='boring'
order by rating DESC;
```





# 626换座位

![image-20240327150208371](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240327150208371.png)

```mysql
select s1.id , IFNULL(s2.student,s1.student) as student
from Seat s1 left join Seat s2 on IF(s1.id%2=1,s1.id = s2.id-1,s1.id = s2.id+1)
```





# 627更换性别

![image-20240328160259431](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240328160259431.png)

```mysql
update Salary
set sex = IF(sex='f','m','f');
```



# 1045买下所有产品的客户

![image-20240328160944990](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240328160944990.png)

```mysql
select customer_id
from Customer
group by customer_id
having count(distinct product_key) = (select count(*) from Product);
```



# 1050合作过至少三次的导演和演员

![image-20240328162435590](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240328162435590.png)

```mysql
select actor_id,director_id
from ActorDirector
group by actor_id,director_id
having count(timestamp)>=3;/0.
```



# 1068产品销售分析Ⅰ

![image-20240328163344063](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240328163344063.png)

```mysql
select Product.product_name ,year,price
from Sales left join Product on Sales.product_id=Product.product_id;
```



# 1068产品销售分析Ⅲ

![image-20240328164644309](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240328164644309.png)

```mysql
select s1.product_id, s1.year as "first_year", quantity, price
from Sales s1,
     (select product_id, min(year) as year from Sales s2 group by product_id) as s2
where s1.product_id = s2.product_id
  and s1.year = s2.year;
```



# 1075项目员工Ⅰ

![image-20240328165121047](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240328165121047.png)

```mysql
select project_id , ROUND(AVG(e.experience_years),2) as "average_years"
from project p,Employee e
where p.employee_id = e.employee_id
group by project_id;
```



	# 1084销售分析Ⅲ

![image-20240328171727222](C:\Users\重逢时\AppData\Roaming\Typora\typora-user-images\image-20240328171727222.png)

```mysql
select s.product_id,p.product_name
from Product p ,Sales s
where p.product_id = s.product_id
group by product_id
having count(s.sale_date) = count(s.sale_date between '2019-01-01' and '2019-03-31'or null);
```

