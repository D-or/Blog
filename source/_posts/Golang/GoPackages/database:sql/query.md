---

title: GoPackages - database/sql 源码解析(4)

---

###查询

**DB.Query：同 SELECT**

```go
func (db *DB) Query(query string, args ...interface{}) (*Rows, error) {
    return db.QueryContext(context.Background(), query, args...)
}

func (db *DB) QueryContext(ctx context.Context, query string, args ...interface{}) (*Rows, error) {
    var rows *Rows
    var err error

    // 如果在强制打开一个新的连接之前，驱动返回错误 driver.ErrBadConn，
    // 通知一个连接断开，maxBadConnRetries 代表最大的重试次数2
    for i := 0; i < maxBadConnRetries; i++ {
        // 从缓存中获取连接
        rows, err = db.query(ctx, query, args, cachedOrNewConn)
        if err != driver.ErrBadConn {
            break
        }
    }

    if err == driver.ErrBadConn {
        // 总是产生新连接
        return db.query(ctx, query, args, alwaysNewConn)
    }

    return rows, err
}

func (db *DB) query(ctx context.Context, query string, args []interface{}, strategy connReuseStrategy) (*Rows, error) {
    // db.conn 获取一个新的或者缓存中的驱动连接，如果出错，则回到 Query 方法的 for 循环中
    dc, err := db.conn(ctx, strategy)
    if err != nil {
        return nil, err
    }

    return db.queryDC(ctx, nil, dc, dc.releaseConn, query, args)
}

func (db *DB) queryDC(ctx, txctx context.Context, dc *driverConn, releaseConn func(error), query string, args []interface{}) (*Rows, error) {
    // driver.Queryer是一个接口，只有 query 方法；从驱动中获取的连接必须实现 Query 方法
    if queryer, ok := dc.ci.(driver.Queryer); ok {
        // .....

        var rowsi driver.Rows
        withLock(dc, func() {
            // 调用 go-sql-driver 中连接的 Query 方法
            // 封装了 queryer.Query(query, dargs)
            rowsi, err = ctxDriverQuery(ctx, queryer, query, dargs)
        })

        if err != driver.ErrSkip {
            if err != nil {
                releaseConn(err)
                return nil, err
            }
            // 将 dc 的所有权转让给 *Rows
            rows := &Rows{
                dc:          dc,
                releaseConn: releaseConn,
                rowsi:       rowsi,
            }

            // ......

            return rows, nil
        }
    }

    // ......
}
```

**DB.conn：获取一个新的或者缓存中的驱动连接**

```go
// conn returns a newly-opened or cached *driverConn.
func (db *DB) conn(ctx context.Context, strategy connReuseStrategy) (*driverConn, error) {
    // 上锁
    db.mu.Lock()

    // 数据库关闭，返回错误
    if db.closed {
        db.mu.Unlock()
        return nil, errDBClosed
    }
    
    // 检查 context 是否过期，过期则返回错误
    select {
    default:
    case <-ctx.Done():
        db.mu.Unlock()
        return nil, ctx.Err()
    }
    
    // 设定生命时间
    lifetime := db.maxLifetime

    // 获取当前空闲连接数量
    numFree := len(db.freeConn)

    // 当空闲连接数量大于 0，则从缓存中获取
    if strategy == cachedOrNewConn && numFree > 0 {
        // 获取空闲连接数组的第一个连接
        conn := db.freeConn[0]
        
        // 重新对空闲连接数组进行赋值？
        copy(db.freeConn, db.freeConn[1:])
        db.freeConn = db.freeConn[:numFree-1]
        
        // 将获取的空闲连接设定为正在使用
        conn.inUse = true
        db.mu.Unlock()
        
        // 判断该连接是否过期，过期则返回错误
        if conn.expired(lifetime) {
            conn.Close()
            return nil, driver.ErrBadConn
        }
        return conn, nil
    }

    // 当不允许打开更多的连接时，发起请求并等待
    if db.maxOpen > 0 && db.numOpen >= db.maxOpen {
        // 创建一个连接请求通道
        req := make(chan connRequest, 1)
        
        // 获取下一个请求的 key
        reqKey := db.nextRequestKeyLocked()
        db.connRequests[reqKey] = req
        db.mu.Unlock()

        select {
        // context 已工作完毕
        case <-ctx.Done():
            // 删除该连接请求
            db.mu.Lock()
            delete(db.connRequests, reqKey)
            db.mu.Unlock()

            select {
            default:
            case ret, ok := <-req:
                if ok {
                    // 往空闲连接池中添加一个连接
                    db.putConn(ret.conn, ret.err)
                }
            }
            return nil, ctx.Err()
        case ret, ok := <-req:
            if !ok {
                return nil, errDBClosed
            }

            // ......

            if ret.err == nil && ret.conn.expired(lifetime) {
                ret.conn.Close()
                return nil, driver.ErrBadConn
            }
            return ret.conn, ret.err
        }
    }

    // 连接打开数量加一
    db.numOpen++ // optimistically
    db.mu.Unlock()

    // 调用驱动的 Open 函数
    ci, err := db.driver.Open(db.dsn)
    if err != nil {
        db.mu.Lock()
        
        // 出错则连接打开数量减一
        db.numOpen--
        
        // 如果已有连接请求，往 connectionOpener 发送数据，产生新连接
        db.maybeOpenNewConnections()
        db.mu.Unlock()
        return nil, err
    }
    db.mu.Lock()
    
    // 将 ci 的所有权转让给 driverConn
    dc := &driverConn{
        db:        db,
        createdAt: nowFunc(),
        ci:        ci,
        inUse:     true,
    }
    
    // 对 dep 进行操作
    db.addDepLocked(dc, dc)
    db.mu.Unlock()
    return dc, nil
}
```

**DB.QueryRow**

```go
// 只返回一条结果，如果没有结果，返回错误 ErrNoRows
func (db *DB) QueryRow(query string, args ...interface{}) *Row {
    return db.QueryRowContext(context.Background(), query, args...)
}

func (db *DB) QueryRowContext(ctx context.Context, query string, args ...interface{}) *Row {
    rows, err := db.QueryContext(ctx, query, args...)
    return &Row{rows: rows, err: err}
}
```

