---

title: GoPackages - database/sql 源码解析(6)

---

```go
// 不返回结果
func (db *DB) Exec(query string, args ...interface{}) (Result, error) {
   return db.ExecContext(context.Background(), query, args...)
}

func (db *DB) ExecContext(ctx context.Context, query string, args ...interface{}) (Result, error) {
   var res Result
   var err error
   for i := 0; i < maxBadConnRetries; i++ {
      res, err = db.exec(ctx, query, args, cachedOrNewConn)
      if err != driver.ErrBadConn {
         break
      }
   }
   if err == driver.ErrBadConn {
      return db.exec(ctx, query, args, alwaysNewConn)
   }
   return res, err
}

func (db *DB) exec(ctx context.Context, query string, args []interface{}, strategy connReuseStrategy) (Result, error) {
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
      dargs, err = driverArgs(dc.ci, nil, args)
      if err != nil {
         return nil, err
      }
      var resi driver.Result
      withLock(dc, func() {
         resi, err = ctxDriverExec(ctx, execer, query, dargs)
      })
      if err != driver.ErrSkip {
         if err != nil {
            return nil, err
         }
         return driverResult{dc, resi}, nil
      }
   }

   var si driver.Stmt
   withLock(dc, func() {
      si, err = ctxDriverPrepare(ctx, dc.ci, query)
   })
   if err != nil {
      return nil, err
   }
   ds := &driverStmt{Locker: dc, si: si}
   defer ds.Close()
   return resultFromStatement(ctx, dc.ci, ds, args...)
}
```

