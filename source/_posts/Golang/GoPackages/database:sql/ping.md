---

title: GoPackages - database/sql 源码解析(3)

---

#### DB.Ping：判断数据库实例是否可用

```go
func (db *DB) Ping() error {
    return db.PingContext(context.Background())
}

func (db *DB) PingContext(ctx context.Context) error {
    var dc *driverConn
    var err error

    for i := 0; i < maxBadConnRetries; i++ {
        // 从空闲数组获取或重新生成一个连接
        dc, err = db.conn(ctx, cachedOrNewConn)
        if err != driver.ErrBadConn {
            break
        }
    }

    if err == driver.ErrBadConn {
        // 总是重新生成一个连接
        dc, err = db.conn(ctx, alwaysNewConn)
    }
    if err != nil {
        return err
    }

    return db.pingDC(ctx, dc, dc.releaseConn)
}

func (db *DB) pingDC(ctx context.Context, dc *driverConn, release func(error)) error {
    var err error

    // driver.Pinger 是一个接口，只有 Ping 方法；从驱动中获取的连接必须实现 Ping 方法
    if pinger, ok := dc.ci.(driver.Pinger); ok {
        withLock(dc, func() {
            // 调用 Ping 方法
            err = pinger.Ping(ctx)
        })
    }
    
    // ......

    return err
}
```

