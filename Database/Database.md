# 175. 组合两个表
<B>题目</B>   
<B>表1: Person</B>  
+-------------+---------+  
| 列名         | 类型     |    
+-------------+---------+  
| PersonId    | int     |  
| FirstName   | varchar |  
| LastName    | varchar |  
+-------------+---------+  
PersonId 是上表主键  
<B>表2: Address</B>   
+-------------+---------+  
| 列名         | 类型    |  
+-------------+---------+  
| AddressId   | int     |  
| PersonId    | int     |  
| City        | varchar |  
| State       | varchar |  
+-------------+---------+  
AddressId 是上表主键  
<B>要求：</B>  
编写一个 SQL 查询，满足条件：无论 person 是否有地址信息，都需要基于上述两表提供 person 的以下信息：
FirstName, LastName, City, State  
<B>分析：</B>  
第一步：两表相连，首先想到连查两张表然后where连接，和左连接然后用on关联;  
第二步：本题需要以Person表为主，考虑到有些Person没有地址信息，不能用where，会过滤掉;  
<B>结论：</B>  
以Person为主表left join 地址信息表Address，然后用on关联。  
on和where条件的区别如下：  
1、on条件是在生成临时表（两表相连，会根据条件生成临时表，然后返回数据）时使用的条件，它不管on中的条件是否为真，都会返回左边表中的记录。  
2、where条件是在临时表生成好后，再对临时表进行过滤的条件。这时已经没有left join的含义（必须返回左边表的记录）了，条件不为真的就全部过滤掉。  
<B>sql：</B>  
```sql
select p.FirstName,p.LastName,a.City,a.State from Person p left join Address a 
on p.PersonId=a.PersonId
```