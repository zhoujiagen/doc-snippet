# mysqld

## 安装后配置

见**MySQL 8.0 Reference Manual** 2.10 Postinstallation Setup and Testing

## main

`sql/main.cc`:


```
extern int mysqld_main(int argc, char **argv);

int main(int argc, char **argv) { return mysqld_main(argc, argv); }
```


`sql/mysqld.cc`:


```
int mysqld_main(int argc, char **argv)
{
  ...
}
```

## Procedures

- my_init()

- load_defaults(...)

- init_variable_default_paths()

- handle_early_options()

- init_sql_statement_names()

- sys_var_init()

- init_error_log()

- mysql_audit_initialize()

- query_logger.init()

- init_common_variables()

- my_init_signals()

- set_ports()

- init_server_components()                          // 初始化server组件

if(opt_bin_log) START

- gtid_state->get_executed_gtids()
- gtid_state->get_lost_gtids()
- gtid_state->get_gtids_only_in_table()
- gtid_state->get_previous_gtids_logged()
- ...
- mysql_bin_log.write_event_to_binlog_and_sync(&prev_gtids_ev)

if(opt_bin_log) END

- init_ssl_communication()
- network_init()                                    // 创建Connection_acceptor

- create_pid_file()

- reload_optimizer_cost_constants()

- mysql_component_infrastructure_init()
- mysql_rm_tmp_tables()
- dynamic_privilege_init()
- servers_init(false)
- init_status_vars()
- check_binlog_cache_size(NULL)
- check_binlog_stmt_cache_size(NULL)
- binlog_unsafe_map_init()
- initialize_information_schema_acl()
- persisted_variables_cache.set_persist_options()
- flush_error_log_messages()
- process_bootstrap()
- start_handle_manager()                            // 创建manager线程

- create_compress_gtid_table_thread()
- server_components_initialized()                   // 标记server已启动
- mysqld_socket_acceptor->connection_event_loop()   // 循环: 监听连接事件, 处理新连接
- terminate_compress_gtid_table_thread()
