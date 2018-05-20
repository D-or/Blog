---

title: GoPackages - database/sql 源码解析(5)

---

### DB.Exec

与 Query不同，它不返回结果，返回 Result

**Result**

```go
type Result interface {
    // 通常，插入新一行的数据时，自动增加，并不是所有的驱动都会实现该方法，语法也各不相同
    LastInsertId() (int64, error)
    
    // 受影响的行数
    RowsAffected() (int64, error)
}
```



```go
func (db *DB) Exec(query string, args ...interface{}) (Result, error) {
    return db.ExecContext(context.Background(), query, args...)
}

func (db *DB) ExecContext(ctx context.Context, query string, args ...interface{}) (Result, error) {
    var res Result
    var err error
    for i := 0; i < maxBadConnRetries; i++ {
        // ...
        res, err = db.exec(ctx, query, args, cachedOrNewConn)
        if err != driver.ErrBadConn {
            break
        }
    }
    if err == driver.ErrBadConn {
        // ...
        return db.exec(ctx, query, args, alwaysNewConn)
    }
    return res, err
}

func (db *DB) exec(ctx context.Context, query string, args []interface{}, strategy connReuseStrategy) (Result, error) {
    // 获取驱动连接
    dc, err := db.conn(ctx, strategy)
    if err != nil {
        return nil, err
    }
    return db.execDC(ctx, dc, dc.releaseConn, query, args)
}

func (db *DB) execDC(ctx context.Context, dc *driverConn, release func(error), query string, args []interface{}) (res Result, err error) {
    defer func() {
        release(err)
    }()
    if execer, ok := dc.ci.(driver.Execer); ok {
        var dargs []driver.NamedValue

        // ...

        var resi driver.Result
        withLock(dc, func() {
            // 驱动实现了接口 Execer 中的函数 Exec，而 ctxDriverExec 则对其进行了封装
            resi, err = ctxDriverExec(ctx, execer, query, dargs)
        })

        if err != nil {
            return nil, err
        }
        
        // 构造 driverResult 并返回
        return driverResult{dc, resi}, nil
    }

    var si driver.Stmt
    withLock(dc, func() {
        // 该方法实现了接口 Conn 的 Prepare 方法，赋值给 si
        si, err = ctxDriverPrepare(ctx, dc.ci, query)
    })
    if err != nil {
        return nil, err
    }
    
    // 构造 driverStmt
    ds := &driverStmt{Locker: dc, si: si}
    defer ds.Close()
    
    // 通过 Statement 获取 Result，该方法中的 ctxDriverStmtExec 方法实现了接口 
    return resultFromStatement(ctx, dc.ci, ds, args...)
}
```

###事务

`DB.Begin` 开启一个事务，默认的隔离级别取决于驱动程序，mysql 的默认隔离级别是 Repeatable read (重复读)

```go
func (db *DB) Begin() (*Tx, error) {
    return db.BeginTx(context.Background(), nil)
}

func (db *DB) BeginTx(ctx context.Context, opts *TxOptions) (*Tx, error) {
    var tx *Tx
    var err error
    for i := 0; i < maxBadConnRetries; i++ {
        // ...
        tx, err = db.begin(ctx, opts, cachedOrNewConn)
        if err != driver.ErrBadConn {
            break
        }
    }
    if err == driver.ErrBadConn {
        // ...
        return db.begin(ctx, opts, alwaysNewConn)
    }
    return tx, err
}

func (db *DB) begin(ctx context.Context, opts *TxOptions, strategy connReuseStrategy) (tx *Tx, err error) {
    // ...
    dc, err := db.conn(ctx, strategy)
    if err != nil {
        return nil, err
    }
    return db.beginDC(ctx, dc, dc.releaseConn, opts)
}

func (db *DB) beginDC(ctx context.Context, dc *driverConn, release func(error), opts *TxOptions) (tx *Tx, err error) {
    var txi driver.Tx
    withLock(dc, func() {
        // ...
        txi, err = ctxDriverBegin(ctx, opts, dc.ci)
    })
    if err != nil {
        release(err)
        return nil, err
    }

    // 当 context 被取消时，事务回滚
    // 当 done 字段被置为 true 后，cancle 方法将会被调用
    ctx, cancel := context.WithCancel(ctx)
    tx = &Tx{
        db:          db,
        dc:          dc,
        releaseConn: release,
        txi:         txi,
        cancel:      cancel,
        ctx:         ctx,
    }
    
    // awaitDone 将会一直阻塞，直到如果事务还未结束时，context 被取消，该事务将会回滚
    go tx.awaitDone()
    return tx, nil
}
```

###DB.Close

```go
func (db *DB) Close() error {
    db.mu.Lock()

    // db 已关闭
    if db.closed {
        db.mu.Unlock()
        return nil
    }

    // 关闭通道
    close(db.openerCh)
    if db.cleanerCh != nil {
        close(db.cleanerCh)
    }

    var err error

    // 创建函数切片
    fns := make([]func() error, 0, len(db.freeConn))
    for _, dc := range db.freeConn {
        // closeDBLocked (当 dc.db 的锁已上锁)
        fns = append(fns, dc.closeDBLocked())
    }

    // 清空空闲连接
    db.freeConn = nil

    // ...
    db.closed = true

    // 关闭请求通道
    for _, req := range db.connRequests {
        close(req)
    }

    db.mu.Unlock()
    
    // 调用 fns 中的函数
    for _, fn := range fns {
        err1 := fn()
        if err1 != nil {
            err = err1
        }
    }
    return err
}
```
