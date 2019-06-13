# 创建表Student，包含id（主码），name, tel
    create table if not exists student (
        id integer primary key,
        name text,
        tel text
    )
> 文本用text
> 整型使用integer
> **其它定义，sqlite匹配不到，一律按照text处理**

# 增
    insert into student(id,name,tel) values(101,"Jack","110")
> 使用SQLiteDatabase的insert则是：

    db.insert("QuesStuNote", null, values);// values为ContentValues

# 删表
    drop table if exists student
    
# 删项
    delete from student where id = 1

> 清空表的所有项

    delete from student
    
> 使用SQLiteDatabase的delete则是：

    db.delete("student", "id = ?", new String[]{String.valueOf(1)});
    
# 改
    update update student set name = '张三' where id = 6
> 使用SQLiteDatabase的update则是：

    db.update("student", values, "id = ? and age = ?", new String[]{String.valueOf(1), String.valueOf(10)});
    
# 查
    select * from student order by name desc（desc降序， asc升序）
> 使用SQLiteDatabase的insert则是:

    Cursor cursor =  db.query("student", new String[]{"id,name,age"},
    "namelike = ?", new String[]{"小三"},null,null, "id desc", "1,2");
    student.setId(cursor.getInt(0));
    
> 各参数的含义：
    table：表名。相当于select语句from关键字后面的部分。如果是多表联合查询，可以用逗号将两个表名分开。  
    columns：要查询出来的列名。相当于select语句select关键字后面的部分。  
    selection：查询条件子句，相当于select语句where关键字后面的部分，在条件子句允许使用占位符“?”  
  selectionArgs：对应于selection语句中占位符的值，值在数组中的位置与占位符在语句中的位置必须一致，否则就会有异常。  
    groupBy：相当于select语句group by关键字后面的部分  
    having：相当于select语句having关键字后面的部分  
    orderBy：相当于select语句order by关键字后面的部分，如：id desc, age asc;  
    limit：指定偏移量和获取的记录数，相当于select语句limit关键字后面的部分。  
    
# 修改表名称
> 将teacher这个表重命名为student

    alter table teacher rename to student

# 插入字段名称
> 添加age字段，并默认为10

    alert table student add age integer default 10
    
# 修改字段名称
> 将学生表name字段修改为stu_name

    alert table student rename column name to stu_name;


