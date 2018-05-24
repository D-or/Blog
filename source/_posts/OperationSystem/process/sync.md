---
title: 进程 - 同步
---

### 基本概念

#### 两种形式的制约关系

程序并发执行时，由于资源的共享和进程间的合作，使同处于一个系统的进程间可能存在两种制约关系

- **间接相互制约关系**

  进程共享某种系统资源，CPU、I/O设备 等。例如，资源 R 分配给进程 A，进程 B 只能阻塞，等待进程 A 释放资源 R，进程 B 才会从阻塞状态转变成就绪状态

- **直接相互制约关系**

  源于进程间的合作。例如，进程 A 通过单缓冲向进程 B 提供数据，当缓冲满时，进程 A 不能发送数据而进入阻塞状态，当进程 B 将数据取走时则唤醒进程 A；当缓冲空时，进程 B 不能获取数据而进入阻塞状态，当进程 B 将数据放入时则唤醒进程 B

#### 临界资源

许多硬件资源都属于临界资源，如打印机、磁带机等。进程间采用互斥方式实现资源的共享。

#### 临界区

**临界区**就是进程访问临界资源的代码。进程进入临界区之前，需要检查临界资源是否正被访问，如果未被访问，则进程访问资源并设置其正被访问的标志，如果正被访问，则进程不能进入临界区，而这段检查的代码成为**进入区**。而临界区后面也要加入一段称为**退出区**的代码，以将临界区的标志设为未被访问。进程中出上述进入区、临界区及退出区之外的代码都称为**剩余区**

进程访问临界资源

```
repeat
    entry section;
    critical section;
    exit section;
    remainder section;
until false;
```

#### 同步机制遵循的规则

- **空闲让进**
- **忙则等待**
- **有限等待**
- **让权等待**

---

### 信号量机制

1965由荷兰学者 Dijkstra 提出的一种进程同步工具，从整型信号量经记录型信号量，发展到“信号量集”机制

#### 整型信号量

一个用于表示资源数目的整型量 S，与一般整型量不同，除初始化外，仅能通过两个标准的原子操作 wait(S) 和 signal(S) 来访问，简称 P，V 操作

```
wait(S):
    while S <= 0 do no-op;
    S := S - 1;

signal(S):
    S := S + 1;
```

#### 记录型信号量

整型信号量机制中的 wait 操作，只要信号量 S<=0，就会不断判断，未遵循“让权等待”，而是“忙等”。记录型信号量机制不存在“忙等”，但采取了“让权等待”后，又会出现多个进程等待访问同一个临界资源的情况。因此，除了代表资源数目的整型变量 value 外，还增加了进程链表指针 L，用于链接所有等待进程

```
type semaphore = record
                 value: interger;
                 L: list of process
                 end
```

wait(S) 和 signae(S) 操作

```
procedure wait(S)
    var S: semaphore
    begin
        S.value := S.value - 1;
        if S.value < 0 then block(S.L);
    end

procedure signal(S)
    var S: semaphore
    begin
        S:value := S.value + 1;
        if S.value <= 0 then wakeup(S.L);
    end
```

S.value 初值表示某类资源的数目，又称资源信号量。每次 wait 操作，进程请求一个单位资源，该类资源数减一，因此 S.value = S.value -1，若 S.value < 0 时，表示该类资源已分配完，因此进程 block，并插入到 S.L 中，遵循了“让权等待”。此时 S.value 的绝对值为信号量链表中阻塞进程的数量。而每次 signal 操作，进程释放一个单位资源，该类资源数加一，因此 S.value = S.value + 1，若 S.value <= 0，则表示仍有进程被阻塞，因此调用 wakeup 原语，将 进程链表 S.L 的第一个进程唤醒

#### AND 型信号量

上述的信号量机制都是针对进程间共享一个临界资源而言的，但有些情况下，进程需要获得两个或更多的资源才可执行任务，因此 AND 型信号量就是处理这种情况的机制

例如，进程 A 和 B 都要求访问共享资源 D 和 E，设置信号量 Dmutex、Emutex，初值都为 1，两个进程都要包含对两个信号量的操作

```
process A:
    wait(Dmutex);
    wait(Emutex);

porcess(B):
    wait(Dmutex);
    wait(Emutex);
```

若 A，B交替运行

```
process A: wait(Dmutex);    // Dmutex = 0
process B: wait(Emutex);    // Emutex = 0
process A: wait(Emutex);    // Emutex = -1，A 阻塞
process B: wait(Dmutex);    // Dmutex = -1，B 阻塞
```

此时，两个进程就进入了死锁状态，所以，当进程间要求的共享资源越多发生死锁的可能性越大

AND 同步机制：将进程所需的资源一次性分配给进程，使用完再一次性释放。只要有一个资源没有分配，其他所有的资源就不分配，也就是，要么全部分配，要么一个也不分配。wait 操作中增加了一个 “AND” 条件，称为 AND 同步，或称同时 wait 操作，即 Swait(Simultaneous wait)

```
Swait(S1, S2, ..., Sn)
    if S1 >= 1 and S2 >= 1 and ... and Sn >= 1 then
        for i := 1 to n do
            Si := Si - 1;
        endfor
    else
        place the process in the waiting queue associated with the first Si found with Si < 1,
        and set the program count of this process to the beginnning of Swait operaion.
    endif

Ssignal(S1, S2, ..., Sn)
    for i := 1 to n do
        Si := Si + 1;
        Remove all the process waiting in the queue associated with Si into the ready queue.
    endfor;
```

#### 信号量集

记录型信号量机制中，wait 或 signal 操作只能对信号量加 1 或者减 1，即获得或释放一个单位的资源，而当一次需要 N 个资源时，若进行 N 次 wait(S) 操作，则效率低下。而且，当资源数量低于某一下限值时，就不予分配。因此每次分配之前都需要进行测试。基于上述两点，对 AND 信号量机制进行扩充，即为“信号量集”机制

```
// S 为信号量，d 为需求值，t 为下限值
Swait(S1, t1, d1, S2, t2, d2, ..., Sn, tn, dn)
    if S1 >= t1 and S2 >= t2 and Sn >= tn then
        for i:= 1 to n do
            Si := Si - di;
        endfor
    else
        Place the executing process in the waiting queue of the first Si with Si < ti and set
        its program counter to the beginning of the Swait Opration.
        
 Ssignal(S1, d1, S2, d2, ..., Sn, dn)
     for i := 1 to n do
         Si := Si + di;
         Remove all the process waiting in the queue associated with Si into the ready queue.
     endfor;
```

特殊情况：

- Swait(S, d, d)。只有一个信号量 S，允许申请 d 个资源，现有资源少于 d 时不予分配
- Swait(S, 1, 1)。信号量集转变成一般的记录型信号量（S > 1）或互斥信号量（S = 1）
- Swait(S, 1, 0)。当 S >= 1 时，允许多个进程进入特定区；当 S 变为 0后，将阻止任何任何进程进入特定区

---

### 信号量应用

#### 进程互斥

设置一互斥信号量，初始值为 1，在进程进入临界区之前对 mutex 进行 wait 操作，若该资源未被访问，则 wait 操作成功，此时如果有其他进程欲进入临界区，则对 mutex 的 wait 就会失败，阻塞进程，而当进程离开临界区后，对 mutex 进行 signal 操作

```
Var mutex: semaphore := 1;

begin
    parbegin
        process 1: begin
                       repeat
                           wait(mutex);
                           critical secion;
                           signal(mutex);
                           remainder section;
                       until false;
                   end
        process 2: begin
                       repeat
                           wait(mutex);
                           critical secion;
                           signal(mutex);
                           remainder section;
                       until false;
                   end
    parend
```

#### 前趋关系

假设进程 A、B 并发执行，A 中有语句 S1，B 中有语句 S2，若 S1 执行后才可执行 S2，可设一个信号量 S，初值为 0，则

```
process A:
    S1; signal(S);

process B:
    wait(S); S2;
```

由于 S 初值为 0，若进程 B 先执行则阻塞，只有等进程 A 执行完使 S 加 1，进程 B 才能执行

例如，如下的前趋图

为使各程序能正确执行，需分别为 S1→S2、S1→S3、S2→S4、S2→S5、S3→S6、S4→S6、S5→S6 设置信号量 a、b、c、d、e、f、g，则

```
Var a, b, c, d, e, f, g: semaphore := 0, 0, 0, 0, 0, 0, 0;

begin
    parbegin
        begin S1;      signal(a); signal(b); end;
        begin wait(a); S2;        signal(c); signal(d); end;
        begin wait(b); S3;        signal(e); end;
        begin wait(c); S4;        signal(f); end;
        begin wait(d); S5;        signal(g); end;
        begin wait(e); wait(f);   wait(g);   S6;        end;
    parend
end
```

---

### 管程机制

#### 定义

#### 条件变量

