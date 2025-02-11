# 一、创建MySQL数据库

**SQL 关键字不区分大小写**：例如，`SELECT`、`select`、`SeLeCt` 都是有效的，MySQL 会将它们视为相同的关键字

## 1.查看数据库

```
SHOW DATABASES
```

## 2.创建数据库：

```
CREATE DATABASE menagerie;
```

​	这样子会创建一个叫`menagerie`的数据库，但是如果这个数据库已经存在则可能会出现一些问题，所以当不确定存不存在的时候可以使用下面的语句

```
CREATE DATABASE IF NOT EXISTS menagerie;
```

​	然后使用下面的语句就可以开始使用这个数据库

```
mysql> USE menagerie
```

# 二、数据表

## 1.查看数据表

```
mysql> SHOW TABLES;
```

## 2.创建数据表

```
mysql> CREATE TABLE pet (name VARCHAR(20), owner VARCHAR(20),
       species VARCHAR(20), sex CHAR(1), birth DATE, death DATE);
```

当然，创建数据表也可以像创建数据库那样判断一下是不是已经存在了

```
CREATE TABLE IF NOT EXISTS my_table (
    id INT PRIMARY KEY,
    name VARCHAR(100)
);
```

**数据表创建解析如下：**

​	创建的时候每一个逗号是一项，每一项由字段名以及数据类型组成(最底下讲了类型)

```
CREATE TABLE pet (
    name VARCHAR(20),      -- 字段名 'name'，类型是可变长度字符串（最大 20 个字符）
    owner VARCHAR(20),     -- 字段名 'owner'，类型是可变长度字符串（最大 20 个字符）
    species VARCHAR(20),   -- 字段名 'species'，类型是可变长度字符串（最大 20 个字符）
    sex CHAR(1),           -- 字段名 'sex'，类型是定长字符串（1 个字符，通常用来表示性别 'M' 或 'F'）
    birth DATE,            -- 字段名 'birth'，类型是日期（格式：YYYY-MM-DD）
    death DATE             -- 字段名 'death'，类型是日期（格式：YYYY-MM-DD）
);

```

同时，创建的时候还可以生成一个自动增长的主键

```
CREATE TABLE users (
    id INT PRIMARY KEY AUTO_INCREMENT,  -- 自动增长的主键，通常从1开始
    name VARCHAR(100),
    email VARCHAR(100)
);
```

## 3.查看数据表都有哪些字段

使用describe语句

```
mysql> DESCRIBE pet;  //这个就是查看pet数据表都有哪些东西
```

# 三、添加数据至数据表

## 1.使用LOAD DATA

`LOAD DATA`通常用于将一个txt文件的数据全部放入数据库的数据表中，如下语句：

```
mysql> LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet;
```

​	就是把pet.txt文件的数据加载到pet数据表中，但是注意，我们需要处理换行符，在 Windows 上编辑文本文件，文本行通常会使用 `\r\n` 作为行终止符（回车 + 换行）。所以还要加上一句话，如下所示

```
mysql> LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet
       LINES TERMINATED BY '\r\n';
```

### 字段和数据不一致的情况

如果您提到的 "数据表中的某一字段的数据和文本文件中某条记录的前几个字段一样，但后面几个字段的值不一样" 是指文本文件中的某些数据与表中的某些字段数据有冲突或重复，处理方式取决于您的需求：

- **如果数据在表中已经存在**：

  - **不需要处理**：如果您只是插入数据并希望自动处理重复的主键或唯一约束，可以使用 `IGNORE` 关键字，这样 MySQL 会忽略那些违反约束的行。

    ```
    LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet
    IGNORE
    LINES TERMINATED BY '\r\n';
    ```

    使用 `IGNORE` 时，如果遇到已经存在的记录，MySQL 会跳过它们，而不会报错。

- **如果数据中后几个字段的值不一样**，可以考虑以下几种情况：

  1. **更新现有记录**：如果您希望在记录存在时更新某些字段，可以使用 `ON DUPLICATE KEY UPDATE` 语句。假设 `id` 是主键或唯一索引，您可以使用以下语法：

     ```
     LOAD DATA LOCAL INFILE '/path/pet.txt' INTO TABLE pet
     LINES TERMINATED BY '\r\n'
     ON DUPLICATE KEY UPDATE name = VALUES(name), species = VALUES(species);
     ```

     这样，若 `id` 已存在，则更新 `name` 和 `species` 字段。

  2. **完全覆盖现有记录**：如果您想删除原来的记录并用新数据替换它，您可能需要先删除表中的相关数据，再加载新的数据：

     ```
     DELETE FROM pet WHERE id = 123;  -- 删除特定记录
     ```

     然后使用 `LOAD DATA` 导入新数据。

## 2.使用INSERT

`INSERT`多用于只添加一条数据，**在 `INSERT` 语句中，如果没有明确指定字段名，必须为所有字段提供值**，否则会报错，示例如下：

```
//不给出具体字段
mysql> INSERT INTO pet
       VALUES ('Puffball','Diane','hamster','f','1999-03-30',NULL);
       
//给出具体字段
mysql> INSERT INTO pet (PetName, OwnerName, Species, Sex, BirthDate)
	   VALUES ('Puffball', 'Diane', 'hamster', 'f', '1999-03-30');
在第二种情况下，你没有为 DeathDate 提供值，因此它会使用表定义中的默认行为（如设为 NULL），这样就不会报错。
```

这条 `INSERT` 语句向 `pet` 表插入了一条记录，数据的内容如下：

- **PetName**: `'Puffball'` — 宠物的名字是 "Puffball"。
- **OwnerName**: `'Diane'` — 宠物的主人是 "Diane"。
- **Species**: `'hamster'` — 宠物的种类是 "hamster"（仓鼠）。
- **Sex**: `'f'` — 宠物的性别是 "f"，表示雌性。
- **BirthDate**: `'1999-03-30'` — 宠物的出生日期是 1999年3月30日。
- **DeathDate**: `NULL` — 宠物的死亡日期是 `NULL`，表示宠物尚未去世。

# 四、检索数据表的数据

## 1.SELECT

该[`SELECT`](https://dev.mysql.com/doc/refman/8.0/en/select.html)语句用于从表中提取信息。该语句的一般格式为：

```sql
SELECT what_to_select
FROM which_table
WHERE conditions_to_satisfy;
```

## 2.检索所有数据

```
mysql> SELECT * FROM pet;
+----------+--------+---------+------+------------+------------+
| name     | owner  | species | sex  | birth      | death      |
+----------+--------+---------+------+------------+------------+
| Fluffy   | Harold | cat     | f    | 1993-02-04 | NULL       |
| Claws    | Gwen   | cat     | m    | 1994-03-17 | NULL       |
| Buffy    | Harold | dog     | f    | 1989-05-13 | NULL       |
| Fang     | Benny  | dog     | m    | 1990-08-27 | NULL       |
| Bowser   | Diane  | dog     | m    | 1979-08-31 | 1995-07-29 |
| Chirpy   | Gwen   | bird    | f    | 1998-09-11 | NULL       |
| Whistler | Gwen   | bird    | NULL | 1997-12-09 | NULL       |
| Slim     | Benny  | snake   | m    | 1996-04-29 | NULL       |
| Puffball | Diane  | hamster | f    | 1999-03-30 | NULL       |
+----------+--------+---------+------+------------+------------+
```

## 3.检索特定行

```
mysql> SELECT * FROM pet WHERE name = 'Bowser';
+--------+-------+---------+------+------------+------------+
| name   | owner | species | sex  | birth      | death      |
+--------+-------+---------+------+------------+------------+
| Bowser | Diane | dog     | m    | 1989-08-31 | 1995-07-29 |
+--------+-------+---------+------+------------+------------+
```

### 使用逻辑运算符：

你可以组合条件，例如定位母狗：

```sql
mysql> SELECT * FROM pet WHERE species = 'dog' AND sex = 'f';
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
```

上述查询使用了[`AND`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_and) 逻辑运算符。还有一个 [`OR`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_or)运算符：

```sql
mysql> SELECT * FROM pet WHERE species = 'snake' OR species = 'bird';
+----------+-------+---------+------+------------+-------+
| name     | owner | species | sex  | birth      | death |
+----------+-------+---------+------+------------+-------+
| Chirpy   | Gwen  | bird    | f    | 1998-09-11 | NULL  |
| Whistler | Gwen  | bird    | NULL | 1997-12-09 | NULL  |
| Slim     | Benny | snake   | m    | 1996-04-29 | NULL  |
+----------+-------+---------+------+------------+-------+
```

[`AND`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_and)和 [`OR`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_or)可以混合使用，但 [`AND`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_and)优先级高于 [`OR`](https://dev.mysql.com/doc/refman/8.0/en/logical-operators.html#operator_or)。如果同时使用这两个运算符，最好使用括号明确指示应如何对条件进行分组：

```sql
mysql> SELECT * FROM pet WHERE (species = 'cat' AND sex = 'm')
       OR (species = 'dog' AND sex = 'f');
+-------+--------+---------+------+------------+-------+
| name  | owner  | species | sex  | birth      | death |
+-------+--------+---------+------+------------+-------+
| Claws | Gwen   | cat     | m    | 1994-03-17 | NULL  |
| Buffy | Harold | dog     | f    | 1989-05-13 | NULL  |
+-------+--------+---------+------+------------+-------+
```

## 4.检索特定列

如果您不想查看表格中的整行，只需命名您感兴趣的列，并用逗号分隔。例如，如果您想知道您的动物何时出生，请选择`name`和 `birth`列：

```sql
mysql> SELECT name, birth FROM pet;
+----------+------------+
| name     | birth      |
+----------+------------+
| Fluffy   | 1993-02-04 |
| Claws    | 1994-03-17 |
| Buffy    | 1989-05-13 |
| Fang     | 1990-08-27 |
| Bowser   | 1989-08-31 |
| Chirpy   | 1998-09-11 |
| Whistler | 1997-12-09 |
| Slim     | 1996-04-29 |
| Puffball | 1999-03-30 |
```

使用关键字`DISTINCT`可以过滤掉该列重复出现的值

```
mysql> SELECT DISTINCT owner FROM pet;
+--------+
| owner  |
+--------+
| Benny  |
| Diane  |
| Gwen   |
| Harold |
+--------+
```

您可以使用`WHERE`子句将行选择与列选择结合起来。例如，若要仅获取狗和猫的出生日期，请使用以下查询：

```sql
mysql> SELECT name, species, birth FROM pet
       WHERE species = 'dog' OR species = 'cat';
+--------+---------+------------+
| name   | species | birth      |
+--------+---------+------------+
| Fluffy | cat     | 1993-02-04 |
| Claws  | cat     | 1994-03-17 |
| Buffy  | dog     | 1989-05-13 |
| Fang   | dog     | 1990-08-27 |
| Bowser | dog     | 1989-08-31 |
+--------+---------+------------+
```

## 5.排序后再输出(ORDER BY)

​	**使用子句ORDER BY**

​	以下是按日期排序的动物生日：

```sql
mysql> SELECT name, birth FROM pet ORDER BY birth;
+----------+------------+
| name     | birth      |
+----------+------------+
| Buffy    | 1989-05-13 |
| Bowser   | 1989-08-31 |
| Fang     | 1990-08-27 |
| Fluffy   | 1993-02-04 |
| Claws    | 1994-03-17 |
| Slim     | 1996-04-29 |
| Whistler | 1997-12-09 |
| Chirpy   | 1998-09-11 |
| Puffball | 1999-03-30 |
```

​	默认排序顺序为升序，最小值排在最前面。要按反向（降序）排序，请将关键字添加 **`DESC`**到要排序的列的名称中：

```sql
mysql> SELECT name, birth FROM pet ORDER BY birth DESC;
+----------+------------+
| name     | birth      |
+----------+------------+
| Puffball | 1999-03-30 |
| Chirpy   | 1998-09-11 |
| Whistler | 1997-12-09 |
| Slim     | 1996-04-29 |
| Claws    | 1994-03-17 |
| Fluffy   | 1993-02-04 |
| Fang     | 1990-08-27 |
| Bowser   | 1989-08-31 |
| Buffy    | 1989-05-13 |
+----------+------------+
```

​	当然排序方式可以不止一个，可以对多个列进行排序，并且可以按不同方向对不同的列进行排序。例如，**要按动物类型按升序排序，然后按动物类型内的出生日期按降序排序（最年轻的动物排在前面）**，请使用以下查询：

```sql
mysql> SELECT name, species, birth FROM pet
       ORDER BY species, birth DESC;   //DESC出现的位置影响哪个列
+----------+---------+------------+
| name     | species | birth      |
+----------+---------+------------+
| Chirpy   | bird    | 1998-09-11 |
| Whistler | bird    | 1997-12-09 |
| Claws    | cat     | 1994-03-17 |
| Fluffy   | cat     | 1993-02-04 |
| Fang     | dog     | 1990-08-27 |
| Bowser   | dog     | 1989-08-31 |
| Buffy    | dog     | 1989-05-13 |
| Puffball | hamster | 1999-03-30 |
| Slim     | snake   | 1996-04-29 |
+----------+---------+------------+
```

​	该`DESC`关键字仅适用于紧接在其之前的列名（`birth`）；它不会影响`species`列排序顺序。

































------



# 拓展：

## 一、MySQL数据类型：

### 1. **数字类型**

- **INT**（整数）：用于存储整数值，范围通常是 `-2147483648` 到 `2147483647`（标准大小）。例如：`id INT`。
- **TINYINT**（小整数）：存储较小的整数值，通常在 `-128` 到 `127` 之间。
- **SMALLINT**（小整数）：用于存储比 `INT` 更小的整数值，范围通常是 `-32768` 到 `32767`。
- **MEDIUMINT**（中等整数）：用于存储介于 `SMALLINT` 和 `INT` 之间的整数值。
- **BIGINT**（大整数）：用于存储比 `INT` 更大的整数值，范围通常是 `-9223372036854775808` 到 `9223372036854775807`。
- **FLOAT**（单精度浮点数）：用于存储小数，适合存储不需要太高精度的浮动数字。
- **DOUBLE**（双精度浮点数）：用于存储大范围的浮动数字，精度更高。
- **DECIMAL** 或 **NUMERIC**：用于存储精确的小数值，适合财务和会计应用，定义精度和小数点后的位数。

### 2. **字符串类型**

- **VARCHAR(n)**：变长字符串，最多存储 `n` 个字符。`n` 是指定的最大字符长度。例如，`VARCHAR(100)` 最多可以存储 100 个字符。
- **CHAR(n)**：定长字符串，存储固定长度的字符，如果实际长度小于 `n`，会在右边填充空格。例如，`CHAR(10)` 总是存储 10 个字符，不足时补空格。
- **TEXT**：用于存储大量文本数据，通常比 `VARCHAR` 支持更长的文本，但没有固定长度限制。适用于非常长的文本字段。
- **LONGTEXT**：用于存储非常大的文本数据，可以存储更长的内容，适合存储文章、评论等。

### 3. **日期和时间类型**

- **DATE**：用于存储日期，格式为 `YYYY-MM-DD`。例如，`2025-01-16`。
- **DATETIME**：用于存储日期和时间，格式为 `YYYY-MM-DD HH:MM:SS`。例如，`2025-01-16 14:30:00`。
- **TIMESTAMP**：与 `DATETIME` 类似，但通常与数据库的时区相关联，存储的是从 `1970-01-01 00:00:00 UTC` 到当前时间的秒数。适用于记录创建时间或更新时间。
- **TIME**：用于存储时间，格式为 `HH:MM:SS`。例如，`14:30:00`。
- **YEAR**：用于存储年份，格式为 `YYYY`。例如，`2025`。

### 4. **布尔类型**

- **BOOLEAN**：用于存储 `TRUE` 或 `FALSE`，实际上是 `TINYINT(1)`，`TRUE` 被存储为 1，`FALSE` 被存储为 0。

### 5. **二进制类型**

- **BLOB**（Binary Large Object）：用于存储二进制数据，如图片、音频、视频等。
- **VARBINARY**：变长的二进制数据，类似于 `VARCHAR` 但存储的是二进制数据。

### 6. **枚举和集合类型**

- **ENUM**：枚举类型，允许列中的值是预定义的几个选项之一。适用于如性别（`M` 或 `F`）等字段。例如：`ENUM('M', 'F')`。
- **SET**：集合类型，允许列中的值是预定义的多个选项之一或多个选项的组合。

### 7. **其他类型**

- **JSON**：用于存储 JSON 格式的数据，通常用于存储复杂的结构化数据。

## 二、删除操作

### 1.DROP

在 MySQL 中，删除表和删除数据库的操作是不可逆的，请在执行这些操作之前务必备份相关数据

#### 1.1.删除表

如果您想删除某个表，可以使用 `DROP TABLE` 命令。例如，要删除 `test_table` 表：

```
DROP TABLE test_table;
```

- 删除表时，会删除该表中的所有数据，并且该表无法恢复。
- 如果要删除多个表，可以使用逗号分隔多个表名：

```
DROP TABLE table1, table2, table3;
```

#### 1.2.删除数据库

如果您想删除整个数据库及其包含的所有表，可以使用 `DROP DATABASE` 命令。例如，要删除名为 `test_db` 的数据库：

```
DROP DATABASE test_db;
```

- 删除数据库时，会删除数据库中的所有表和数据，且该数据库无法恢复。
- 如果删除的数据库不存在，MySQL 会报错。

### 2.DELETE

`DELETE` 语句用于从表中删除数据，它有以下几个要点：

- **删除数据**：`DELETE` 语句只删除表中的数据，而不删除表的结构或列。表和表的定义（包括字段、索引等）都会保留，只有数据会被清空。

- **语法**：

  ```
  DELETE FROM table_name WHERE condition;
  ```

  例如：

  ```
  DELETE FROM pet WHERE id = 1;
  ```

  这条语句会删除 `pet` 表中 `id` 等于 1 的行。

- **没有 `WHERE` 条件**：如果没有 `WHERE` 条件，`DELETE` 会删除表中的所有数据。例如：

  ```
  DELETE FROM pet;
  ```

  这条语句会删除 `pet` 表中的所有数据，但表结构保持不变。

## 三、更新操作

​	`UPDATE` 语句与 `DELETE` 语句类似，也是用于修改数据，但它的作用是更新表中的现有数据，而不是删除数据。`UPDATE` 语句的语法和 `DELETE` 语句的语法有些不同。

### `UPDATE` 语句的语法

`UPDATE` 用于更新表中的数据，基本的语法如下：

```
UPDATE table_name
SET column1 = value1, column2 = value2, ...
WHERE condition;  
```

- **`table_name`**：指定要更新的表。
- **`SET column1 = value1, column2 = value2, ...`**：指定要更新的列及其新值。可以同时更新多列。
- **`WHERE`**：条件语句，指定哪些行需要更新。**如果没有 `WHERE` 子句，则所有行都会被更新。**

### 示例

假设我们有一个 `pet` 表，表结构如下：

| id   | name   | age  |
| ---- | ------ | ---- |
| 1    | petter | 3    |
| 2    | tom    | 5    |

1. 如果你想更新 `name` 为 "petter" 的行的 `age` 字段，语句可以写成：

```
UPDATE pet
SET age = 4
WHERE name = 'petter';
```

这条语句会将 `name` 为 "petter" 的行的 `age` 更新为 4，其他列和行保持不变。

1. 如果你想同时更新多列：

```
UPDATE pet
SET age = 4, name = 'petterUpdated'
WHERE name = 'petter';
```

这条语句会将 `name` 为 "petter" 的行的 `age` 更新为 4，并且将 `name` 更新为 "petterUpdated"。