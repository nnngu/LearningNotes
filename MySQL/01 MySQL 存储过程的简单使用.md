# 01 MySQL 存储过程的简单使用

## 首先创建一张 students 表

SQL脚本如下：

```sql
create table students(
    id int primary key auto_increment,
    age int,
    name varchar(20),
    city varchar(20)
) character set utf8;

insert into students values(null, 22, 'lisa', '杭州');
insert into students values(null, 16, 'rock', '上海');
insert into students values(null, 20, 'jack', '深圳');
insert into students values(null, 21, 'rose', '北京');
```

![][1]

![][2]

## 不带参数的存储过程

```sql
-- 查询学生个数
drop procedure if exists select_students_count;

delimiter ;; -- 替换分隔符
	create procedure select_students_count() 
		begin 
			select count(id) from students; 
		end ;;
delimiter ;
```

执行存储过程：

```sql
call select_students_count();
```

![][3]

## 带参数的存储过程

MySQL 支持 in (传递给存储过程)，out (从存储过程传出) 和 inout (对存储过程传入和传出) 类型的参数。存储过程的代码位于 begin 和 end 语句内，它们是一系列 select 语句，用来检索值，然后保存到相应的变量 (通过指定 into 关键字)

```sql
-- 根据城市查询总数
delimiter ;;
	create procedure select_students_by_city_count(in _city varchar(255))
		begin
			select count(id) from students where city = _city;
		end;;
delimiter ;
```

执行存储过程：

```sql
call select_students_by_city_count('上海');
```

![][4]

## 带有输出参数的存储过程

```sql
-- 根据姓名查询学生信息，返回学生的城市
delimiter ;;
create procedure select_students_by_name(
    in _name varchar(255),
    out _city varchar(255), -- 输出参数
    inout _age int(11)
)
    begin 
    	select city from students where name = _name and age = _age into _city;
    end ;;
delimiter ;
```

执行存储过程：

```sql
set @_age = 20;
set @_name = 'jack';
call select_students_by_name(@_name, @_city, @_age);
select @_city as city, @_age as age;
```

![][5]

## 带有通配符的存储过程

```sql
delimiter ;;
create procedure select_students_by_likename(
    in _likename varchar(255)
)
    begin
    	select * from students where name like _likename;
    end ;;
delimiter ;
```

执行存储过程：

```sql
call select_students_by_likename('%s%');
call select_students_by_likename('%j%');
```

![][6]

## 使用存储过程进行增加、修改、删除

### 增加




### 修改




### 删除



















---

本文永久更新地址：[https://github.com/nnngu/LearningNotes/blob/master/MySQL/01%20MySQL%20%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E7%9A%84%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8.md](https://github.com/nnngu/LearningNotes/blob/master/MySQL/01%20MySQL%20%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E7%9A%84%E7%AE%80%E5%8D%95%E4%BD%BF%E7%94%A8.md)


  [1]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/23/1519381595914.jpg
  [2]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/23/1519381659110.jpg
  [3]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/23/1519382375498.jpg
  [4]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/23/1519382991880.jpg
  [5]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/23/1519384184008.jpg
  [6]: https://www.github.com/nnngu/FigureBed/raw/master/2018/2/23/1519385002267.jpg