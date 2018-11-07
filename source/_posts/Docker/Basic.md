---
title: Docker 常用命令
---

> 与 Docker 进行交互的基本知识

#### 确保 Docker 就绪

```shell
docker info
```

#### 运行容器

```shell
docker run
```

- `-i`

  保证容器 **STDIN** 开启，即持久的标准输入

- `-t`

  为容器分配一个伪 **tty** 终端

- `--name <NAME>`

  给容器命名，命名规则：[a-zA-Z0-9_.-]

- `-d`

  守护式容器，即将容器放在后台运行

- `--log-driver="<json-file(default)/syslog/none>"`

  使用 **syslog** 将禁用 `docker logs` ，使用 **none** 将禁用所有容器的日志（导致 `docker logs` 也被禁用）

- `--restart=<always/on-failure:COUNT>`

  **always：**无论容器的退出代码是什么，Docker 都会自动重启容器；**on-failure:COUNT：**当容器退出代码为非 0 时，Docker 会自动重启容器，最多 **COUNT** 次

- 更多请使用 `docker help run` 或者 `man docker-run` 查看

#### 查看容器

```shell
docker ps
```

- `-a`

  列出所有容器，包括正在运行或已经停止的

- `-q`

  只返回容器的 ID

#### 启动/停止/重启容器

```shell
docker start/stop/restart <NAME/ID>
```

#### 附着到容器的会话上

```shell
docker attach <NAME/ID>
```

#### 查看容器日志

```shell
docker logs <NAME/ID>
```

- `-f`

  监控容器的日志，与 `tail -f` 非常相似，`Ctr+C` 退出日志跟踪

- `--tail <NUMBER>`

  获取日志的最后 **NUMBER** 行内容

- `-t`

  为每一条日志添加时间戳

#### 查看容器内的所有进程

```shell
docker top <NAME/ID>
```

#### Docker 统计信息

```shell
docker stats <NAME_1/ID_1> <NAME_2/ID_2> <NAME_3/ID_3> ...
// 容器名字、CPU、内存、网络I/O、存储I/O性能和指标
```

#### 在容器内部运行进程

```shell
docker exec
```

- `-t -i`

  创建 **TTY** 并捕捉 **STDIN**

- `-d <COMMAND>`

  运行一个后台进程，即在容器内部执行 `COMMAND`

#### 获得更多的容器信息

```shell
docker inspect <NAME/ID>
// 配置信息：名称、命令、网络配置等等
```

- `--format='{{...}}' <NAME_1/ID_1> <NAME_2/ID_2> <NAME_3/ID_3> ...`

  - `--format='{{ .State.Running }}'`

    查看容器状态

  - `--format='{{ .NetworkSettings.IPAddress }}'`

    查看容器 IP 地址

#### 删除容器

```shell
docker rm <NAME/ID>
```

- `-f`

  可运行中的运行中的容器，否则需要先使用 `docker stop ` 或 `docker kill` 停止容器