---
title: 进程 - 经典同步问题
---

### 生产者 - 消费者问题

**利用记录型信号量**

```
Var mutex, empty, full: semaphore := 1, n, 0;
    buffer:array[0, ..., n - 1] of item;
    in, out: integer := 0, 0;

begin
    parbegin
        proceducer: begin
                        repeat
                            ...
                            producer an item nextp;
                            ...
                            wait(empty);
                            wait(mutex);
                            buffer(in) := nextp;
                            in := (in + 1) mod n;
                            signal(mutex);
                            signal(full);
                        until false;
                    end
        cundumer:   begin
                        repeat
                            wait(full);
                            wait(mutex);
                            nextc := buffer(out);
                            out := (out + 1) mod n;
                            signal(mutex);
                            signal(empty);
                            consumer the item in nextc;
                        until false;
                    end
    parend
end
```



### 哲学家进餐问题

### 读者 - 写者问题

