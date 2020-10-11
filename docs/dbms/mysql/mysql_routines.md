# MySQL Routines

- connection handling
- sql parse
- execution plan generation
- sql data write path
- sql data read path
- transaction process
- replication

## 连接建立与处理


连接建立:

- network_init(): sql/mysqld.cc mysqld_main()
- mysqld_socket_acceptor->connection_event_loop(): // sql/mysqld.cc mysqld_main()<br>Connection_acceptor类: sql/conn_handler/connection_acceptor.h

连接处理器:

- One_thread_connection_handler
- Per_thread_connection_handler
- Plugin_connection_handler


handle_connection:

``` C++
// sql/conn_handler/connection_handler_per_thread.cc

while (thd_connection_alive(thd)) {
  if (do_command(thd)) break;         // sql/sql_parse.cc
}
```

## SQL解析

## 执行计划生成

## SQL数据写路径

## SQL数据读路径

## 事务处理

## 复制
