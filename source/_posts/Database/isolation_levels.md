---

title: 数据库 - 四种隔离层级

---

## 数据库四种隔离级别

隔离层级决定/影响用户并发读写时的行为及其结果。读是一般指 `SELSECT` 语句，写是指使数据库内容发生改变的一切语句（`INSERT`，`UPDATE`，`DELETE` 等）

**用法**

```sql
SET TRANSACTION ISOLATION LEVEL <isolation name>;
```

###Read uncommitted


读未提交，就是一个事务可以读取另一个未提交事务的数据

存在问题：**脏读**

例如，窗口1开启一个事务，修改表 Table 中的数据

```sql
BEGIN TRAN;

UPDATE db.Table SET count = count + 1.00 WHERE key = 1;

SELECT key, count FROM db.Table WHERE key = 1;
```

结果为：

```sql
key | count
-----------
1   | 10
```

窗口2查询刚才被修改的数据

```sql
SET TRANSACTION ISOLATION LEVEL READ UNCOMMITTED;

SELECT key, count FROM db.Table WHERE key = 1;
```

结果为：

```sql
key | count
-----------
1   | 10
```

但是注意，由于窗口1的事务还未提交，事务可能回滚 `ROLLBACK TRAN;`，若回滚，再次查询数据，则结果将为9，所以窗口2查询到的数据是脏数据；倘若不想读到脏数据，就需要下面的隔离层级 `Read committed`

###Read committed

`Sql Server` 的默认隔离层级

读已提交，就是一个事务只能读取已提交的事务的数据。也就是说，当更新某一行的事务还未结束时，试图读取该行数据的操作将会一直阻塞，直至更新事务结束才能 Unblock

例如，同样的，窗口1修改表中的数据

```sql
BEGIN TRAN;

UPDATE db.Table SET count = count + 2.00 WHERE id= 2;

SELECT id, count FROM db.Table WHERE id = 2;
```

结果为：

```sql
id | count
-----------
2  | 20
```

同样的，窗口2查询刚才被修改的数据

```sql
SET TRANSACTION ISOLATION LEVEL READ COMMITTED;

SELECT id, count FROM db.Table WHERE id = 2;
```

由于窗口1的事务还未结束，所以窗口2的 `SELECT ` 操作会一直阻塞

回到窗口1执行：

```sql
COMMIT TRAN
```

回到窗口2，可以发现被阻塞的 `SELECT` 操作执行完毕，结果为：

```sql
id | count
-----------
2  | 20
```

### Repeatable read

`Mysql` 的默认隔离层级

重复读，确保数据在读取的过程中不会发生任何变化，在整个事务期间该数据都是被保护的，不允许写操作

例如，同样的，窗口1开启一个事务，先查询表中的数据

```sql
SET TRANSACTION ISOLATION LEVEL REPEATABLE READ;

BEGIN TRAN;

SELECT id, count FROM db.Table WHERE id = 3;
```

结果为：

```sql
id | count
-----------
3   | 36
```

同样的，窗口2修改表中的数据

```sql
UPDATE db.Table SET count = count + 4.00 WHERE id = 3;

SELECT id, count FROM db.Table WHERE id = 3;
```

由于窗口1的事务还未结束，所以窗口2的代码会一直阻塞

回到窗口1执行：

```sql
SELECT id, count FROM db.Table WHERE id = 3;

COMMIT TRAN
```

窗口1第一次查询结果和第二次查询结果一致，说明该行在整个事务中是没有改动的

回到窗口2，可以发现被阻塞的 `SELECT` 操作执行完毕，结果为：

```sql
id | count
-----------
3   | 40
```

###Serializable

可串行化

`Repeatable read` 可以确保某一条数据不被修改，但若是想保证满足 `SELECT` 的数据在读取事务的过程中不被插入，就需要 `Serializable` 这一隔离层级

例如，窗口1查询数据

```sql
SET TRANSACTION ISOLATION LEVEL SERIALIZABLE;

BEGIN TRAN;

SELECT id, count, price FROM db.Table WHERE price = 40.0;
```

查询结果有12条

同样的，窗口2修改表中的数据

```sql
INSERT INTO db.Table (count, price) VALUES(40, 40.0);

SELECT id, count, price FROM db.Table WHERE price = 40.0;
```

由于窗口1的事务还未结束，所以窗口2的代码会一直阻塞

回到窗口1执行：

```sql
SELECT id, count, price FROM db.Table WHERE price = 40.0;

COMMIT TRAN
```

返回的查询结果还是12条，说明数据在事务中没有改动

回到窗口2，可以发现被阻塞的 `Insert` 操作执行完毕，返回结果为13条数据
