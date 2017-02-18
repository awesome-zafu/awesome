### 那就不要怪我功利了

### 数据库基础概念

* DBS DBMS DB 关系: DBS 包括 DBMS 和 DB


* 数据库管理系统是 **应用系统**


* 数据库中,数据的物理独立性是指: **用户的应用程序与存储在磁盘上的数据库中的数据是相互独立的**


* **数据库系统** 是存储在计算机内有结构的数据集合


* 数据库设计中概念结构设计的主要工具是 **E-R图**


* 数据库管理系统的主要功能有 **数据定义功能**、**数据操纵功能**、数据库的运行管理和数据库建立与维护等4个方面。


* E＿R图中，实体之间的联系可抽象为三类，它们是 **一对一**、**一对多** 和 **多对多**。


* 数据模型是由 **数据操纵**、 **数据结构** 和 **数据约束条件** 三部分组成的。

* 数据库是长期存储在 **计算机内**、有 **组织** 的可 **共享** 的数据集合。

* 数据库管理技术经历了 **人工管理**、**文件系统** 和　**数据库系统** 三个阶段

* **数据定义功能** 是对数据系统的静态特性的描述， **数据操纵功能** 是对数据库系统的动态特性的描述。

### ORACLE 逻辑 物理结构

* 如果修改的数据库文件（.dbf）的位置，需要启动数据库到`mount`阶段后修改CONTROL File的信息

* 在ORACLE的安全策略中，`ARCHIVELOG`模式更安全，比 `NOARCHIVELOG`安全

* ORACLE中的，若想查找学生的某个学生信息，采用`SELECT * FROM S WHERE NAME=&name` 形式比`SELECT * FROM S WHERE NAME='xxx'`更好   (不是很懂。)

* 在ORACLE中，所有用户共享的内存区是 `SGA`（System Global Area 系统全局域）

* 数据库实例（INSTANCE）通常包括了 `SGA` 和 **后台进程**

* 控制文件中包含了 重做日志 和 数据文件的目录及其文件名

* SGA 通常包括 ： **SQL cache , Database cache , redo log cache , Data Dictionary cache .**

* REDO LOG File 至少包含 **2** 个组

* ORACLE 启动数据库通常分为三个阶段 **NOMOUNT**、 **MOUNT** 和　**OPEN**。

* 根据不同的情况，ORACLE数据库可以采用不同的关闭方式，分别为 **SHUTDOWN NORMAL/IMMEDIATE/TRANSACTIONAL/ABORT**  　　

## 表与表空间管理

* 删除表空间DATA和及其对应的数据文件 。

```SQL
  DROP TABLESPACE DATA INCLUDING CONTENTS AND DATAFILES;
```

* 把一个新的数据文件添加到DATA表空间 并指定 AUTOEXTEND ON 。

```SQL
  ALTER TABLESPACE DATA ADD DATAFILE $DATAFILE_PATH SIZE 50M  
REUSE AUTOEXTEND ON
NEXT 50M MAXSIZE 500M;
```

* 创建大小为50M的表空间,并禁止自动扩展。

```SQL
CREATE TABLESPACE TEST LOGGING DATAFILE $DATAFILE_PATH SIZE 50M
```

* 修改表空间DATA离线

```SQL
ALTER TABLESPACE DATA OFFLINE
```
*  SYSTEM表空间：存储Oracle的系统数据（如数据字典表）。创建数据库时，SYSTEM第一个被创建，具有较高的管理权限，不能被脱机

* 段是由许多个区间组成，它是一个独立的逻辑存储结构

* 区间是数据库存储空间中分配的一个逻辑单元，由一组相邻的数据块组成，它是Oracle分配磁盘空间的最小单位

* 数据块是Oracle管理数据库存储空间的最小数据存储单位


* 表空间是Oracle数据库中最大的逻辑结构

> NOTE: 磁盘块 -> 数据块 -> 区间 -> 段  
　　
* Oracle 11g数据库有4种类型的段，分别为：**数据段**、**索引段**、**临时段** 和 **回滚段**

* 表空间和数据库物理结构联系密切。它在物理上与磁盘上的数据文件相对应。数据逻辑上存放在表空间中；物理上存放在数据文件中

### SQL操作

* 查询当前用户所有表和视图

```SQL
  select * from tab
```

* 创建一个表，其结构和内容与EMP一样

```SQL
  CREATE TABLE myTable AS SELECT * FROM EMP
```

* 显示回收站的状态

```SQL
  SHOW PARAMETER RECYCLEBIN
```

* 清空回收站中的表myTable

```SQL
  PURGE table myTable
```

* merge into

```SQL
  MERGE INTO myTable E1
USING EMP E2
ON (E1.EMPNO=E2.EMPNO)
WHEN MATCHED THEN
UPDATE SET *E1.SAL=E2.SAL
WHEN NOT MATCHED THEN INSERT (E2.EMPNO,E2.ENAME,E2.JOB,E2.MGR,E2.HIREDATE,E2.SAL,E2.COMM,E2.DEPTNO);
```

* 查询emp表结构

```SQL
  DESC emp
```

* 修改EMP表ENAME字段

```SQL
ALTER TABLE EMP MODIFY ENAME nvarchar2(12)
```

* 修改回收站状态为ON

```SQL
  ALTER SESSION SET RECYCLEBIN=ON;
```

* 将已DROP的myTable表闪回到删除前

```SQL
  FLASHBACK TABLE myTable To BEFORE DROP ;
```

### PL/SQL 基础


### ORACLE文件管理

* `Alter database add logfile group 5(‘文件1’,’文件2,’文件3’) size 20m;`
表示 添加了一个日志文件5 其中包含3个成员 3个成员是一致的 。

* 将数据库从非归档模式修改为归档模式，需要在 **mount** 状态下修改其相应的语句为：`alter database archivelog`

* 如果想修改的日志为当前组，可使用命令：`alter database switch logfile`

* 增加或减少控制文件后，需要在 **Spfile或者Pfile** 中进行修改相应参数    

* 修改控制文件的指针，使控制文件知道日志文件已经处于新的位置了:

```SQL
ALTER database rename file '..old' to '...new'
```           

* 从ORACLE内部结构看，其最主要的文件有 **数据文件**、**日志文件**、**控制文件**

* 同一个ORACLE数据的多个控制文件的内容是 **完全一样**  (NOTE:控制文件都是镜像文件。)

* 重做日志文件位置发生修改后，需要修改 **控制文件** 中的相关参数

* 当前正在写入的联机重做日志文件组的状态status会显示为 **current**

* 数据库只有处于 **归档** 模式，才会对重做日志文件进行归档`archive log list`;  可以查看归档模式

* 控制文件在 **mount** 阶段读取

* 联机重做日志文件是 **循环** 写入的

* 日志文件又被称为重做日志文件（redo log file），用来记录Oracle数据库中的 **全部** 更改操作

* ORACLE中新创建一个用户(用户中u1, 密码u1)的命令 ：
`Create user u1 identified by u1;`

* 新建立的用户要连接数据库，必须具备 **session** 权限，可用如下语句授权：grant create **session** to u1;

* 帐户解锁的命令 : `alter user u1 account unlock`

* profile文件可以用于管理登录参数命令：

```SQL
create PROFILE p1
limit FAILED_LOGIN_ATTEMPTS 3 --失败尝试次数为3次
PASSWORD_LOCK_TIME  2; --密码锁定天数为2天
```

* 利用表闪回可以恢复表，取消对表所进行的修改。表闪回要求用户具有以下哪几个权限 :

    * FLASHBACK ANY TABLE权限或者是该表的Flashback对象权限
    * 有该表的SELECT、INSERT、DELETE和ALTER权限
    * 必须保证该表ROW MOVEMENT。


* 闪回时间是可以修改的:

```SQL
ALTER SYSTEM SET UNDO_RETENTION=XXXX;   ---XXXX单位为秒
```

* 闪回数据 :

```SQL
FLASHBACK DATABASE TO TIMESTAMP(TO_DATE(‘2006-06-01 15:06:16’,'YYYY-MM-DD HH24:MI:SS'));
```

* 如果通过DROP语句删除掉一个基本表，就要用 **删除** 闪回进行恢复


* 要将已经查询闪回的数据插入到表TABLE1中

```SQL
INSERT INTO TABLE1
SELECT * FROM TABLE1
AS OF TIMESTAMP TO_TIMESTAMP
('2011-11-15 11:20:34','YYYY-MM-DD HH24:MI:SS');
```

* 要对误操作进行撤销，其一是依赖于之前的联机 **重做日志** 文件，对数据库进行恢复操作；其二就是 **闪回** 技术。

* 要使表具有ROW MOVEMENT权限 :

```SQL
ALTER TABLE TABLE2 ENABLE ROW MOVEMENT;
```

* 使用删除回闪从回收站恢复表XS_KC1

FLASHBACK TABLE XS_KC1 TO BEFORE DROP;

* 利用UNDO表空间可进行恢复。
UNDO信息的设置将影响到查询闪回，主要是影响可闪回的 时间。
查询UNDO信息的命令为:SHOW PARAMETER UNDO
