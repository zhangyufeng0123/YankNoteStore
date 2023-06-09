# Sql-建表

## 数据定义

### CREATE TABLE

> 定义基本表

```mysql
CREATE TABLE <表名> (<列名> <数据类型> [列级完整性约束条件]
                        [, <列名> <数据类型> [列级完整性约束条件]]
                        ...
                        [, <表级完整性约束条件>]);
```
- `primary key (A1, A2, A3, ...)`：指定主键属性集
- `foreign key (A1, A2, A3, ...) references T2`:声明并表示关系中任意元素在属性`(A1, A2, A3, ...)`上的取值必须对应于`T2`中某元组在主码属性上的取值

**数据类型**：
- `int`:整型。等价于全程`integer`
- `smallint`:小型整数
- `real`, `double precision`:浮点数与双精度浮点数（精度与机器相关）
- `float(n)`:精度至少为n位的浮点数
- `char(n)`:固定长度的字符串
- `varchar(n)`:可变长度的字符串

例如：建立一个“学生信息”表Student：

```mysql
CREATE TABLE Student(
    Sno CHAR(9) PRIMARY KEY,
    Sname CHAR(20) UNIQUE
    Ssex CHAR(2),
    Sage SMALLINT,
    Sdept CHAR(20)
);
```

### ALTER TABLE

> 修正基本表

```mysql
ALTER TABLE <表名>
[ADD <新列名> <数据类型>[完整性约束]]
[DROP <完整性约束>]
[MODIFY COLUMN <列名> <数据类型>];
```

- `ADD`子句：增加新列和新的完整性约束
- `DROP`子句：删除指定的完整性约束
- `MODIFY COLUMN`子句：修改原有列的定义，包括列名和数据类型


例子：
```mysql
ALTER TABLE Student ADD S_entrance DATE;    // 向Student表增加“入学时间”列，其数据类型为日期型
ALTER TABLE Student MODIFY COLUMN INT;      // 将年龄的数据类型由字符串型改为整数
ALTER TABLE ADD UNIQUE(Sname);              // 增加Student表Sname必须取唯一值的约束条件
```

### DROP TABLE

> 删除基本表

```mysql
DROP TABLE <表名> [RESTRICT | CASCADE];
```

- `RESTRICT`: 删除是有限制条件的。欲删除的基本表不能杯其他表的约束所引用（如：check、foreign key等约束），不能有视图，不能有触发器，不能有存储过程或函数等。如果存在这些依赖该表的对象，则该表不能被删除。
- `CASCADE`: 删除没有条件限制。在删除该表的同时，相关的依赖对象，例如试图，都将被一起删除

## 数据类型

### 整型

分类

tinyint, smallint, mediumint, int/integer, bigint(1,2,3,4,8)

特点

1. 如果不设置无符号还是有符号，默认是有符号，如果想设置无符号，需要添加unsigned关键词
2. 如果插入的数值超出了整型的范围，会报out of range异常，并且插入临界值 
3. 如果不设置长度，会有默认的长度

设置有符号和无符号

```mysql
CREATE TABLE tab_int(
	t1 INT,
    t2 INT UNSIGNED
);
```

### 小数

分类：

1. 浮点型 
	- FLOAT(M, D)
    - DOUBLE(M, D)
2. 定点型 
	- DEC(M, D)
    - DECIMAL(M, D)
    - 
特点：
	- M:整数部位+小数部位 
    - D:小数部位
如果超过范围，则插入临界值 
    
M和D都可以省略
如果是DEMICAL， 则M默认为10，D默认为0
如果是FLOAT和DOUBLE，则会根据插入的数值的精度来决定精度 
定点型的精确度较高，如果要求插入数值的精度较高如货币运算等则考虑使用

原则：
	- 所选择的类型越简单越好 
    - 能保存数值的类型越小越好

### 字符型

较短的文本： 
- CHAR
- VARCHAR

较长的文本： 
- TEXT
- BLOB（较长的二进制） 

特点

|  | 写法 | M的意思 | 特点 | 空间的耗费 | 效率 |
| -- | -- | -- | -- | -- | -- |
| char | char(M) | 最大的字符数，可以省略，默认为1 | 固定长度的字符 | 比较耗费 | 高 |
| varchar | varchar(M) | 最大的字符数，不可以省略 | 可变长度的字符 | 比较节省 | 低 |

### 常见约束

**含义**：一种限制，用于限制表中的数据，为了保证表中的数据的准确和可靠性 

**分类：六大约束**
1. **NOT NULL**: 非空，用于保证该字段的值不能为空 比如主键 
2. **DEFAULT**:默认，用于保证该字段有默认值 比如性别
3. **PRIMARY KEY**:主键。用于保证该字段的值具有唯一性，并且非空 
4. **UNIQUE**：唯一，用于保证该字段的值具有唯一性，可以为空，比如邮箱
5. **CHECK**：检查约束 [mysql中不支持]
6. **FOREIGN KEY**:外键，用于限制两个表的关系，用于保证该字段的值必须来自于主表的关联列的值，在从表添加外键约束，用于引用主表中某列的值 比如学生表的专业编号，用工表的部门编号 

添加约束的时机：
1. 创建表时 
2. 修改表时 

约束的添加分类： 
- 列级约束:六大约束语法上都支持，但外键约束没有效果 
- 表记约束：除了非空、默认，其他都支持 

主键和唯一的区别

|  | 保证唯一性 | 为空 | 一个表中可以有多少个 | 是否允许组合 |
| -- | -- | -- | -- | -- |
| 主键 | ✔ | × | 至多有一个 | 是，但不推荐 |
| 唯一 | ✔ | ✔ | 可以有多个 | 是，但不推荐 |

外键：
1. 要求再从表设置外键关系 
2. 从表的外键列的类型和主表的关联列的类型要求一致或兼容
3. 主表的关联列必须是一个key（一般是主键或唯一） 
4. 插入数据时，先插入主表，再插入从表，删除数据时，先删除从表，在删除主表 

