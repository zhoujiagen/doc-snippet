# MySQL源码构建


## 编译

不同的版本在Mac下有不同的编译错误信息, 可以参照时事修正文档.

Version: 8.0.19.

## 项目结构

### MySQL源码结构

```

├─client                          客户端库
│  ├─base
│  ├─check
│  ├─dump
│  ├─mysqltest
│  └─upgrade
├─cmake                           CMake脚本
│  ├─build_configurations
│  └─os
├─components                      组件安装的CMake脚本
│  ├─audit_api_message_emit
│  ├─example
│  ├─library_mysys
│  ├─logging
│  ├─mysqlbackup
│  ├─mysql_server
│  ├─pfs_component
│  ├─pfs_example
│  ├─test
│  └─validate_password
├─Docs                            文档
├─doxygen_resources               Doxygen资源
│  └─storage
├─extra                           额外独立的工具程序
│  ├─duktape
│  ├─icu
│  ├─libedit                        命令行工具
│  ├─libevent
│  ├─lz4
│  ├─protobuf
│  ├─rapidjson
│  ├─zlib                           数据压缩库
│  └─zstd
├─include                         大多数库所需的头文件
│  ├─boost_1_70_0
│  └─mysql
├─libbinlogevents                 binlog事件库
│  ├─export
│  ├─include
│  └─src
├─libbinlogstandalone             独立binlog库
│  └─src
├─libmysql                        将MySQL生成库, 例如Windows .DLL
│  ├─authentication_ldap
│  └─authentication_win
├─libservices                     服务库: 改为使用include/mysql/components
├─man                             用户手册页
├─mysql-test                      mysqld的测试套件
│  ├─collections
│  ├─extra
│  ├─include
│  ├─lib
│  ├─r
│  ├─std_data
│  ├─suite
│  └─t
├─mysys                           MySQL系统库: 文件访问等底层程序
├─packaging                       打包工具
│  ├─deb-in
│  ├─rpm-common
│  ├─rpm-docker
│  ├─rpm-fedora
│  ├─rpm-oel
│  ├─rpm-sles
│  ├─solaris
│  └─WiX
├─plugin                          插件
│  ├─audit_null
│  ├─auth
│  ├─clone
│  ├─connection_control
│  ├─daemon_example
│  ├─ddl_rewriter
│  ├─fulltext
│  ├─group_replication
│  ├─innodb_memcached
│  ├─keyring
│  ├─keyring_udf
│  ├─password_validation
│  ├─pfs_table_plugin
│  ├─replication_observers_example
│  ├─rewriter
│  ├─rewrite_example
│  ├─semisync
│  ├─test_services
│  ├─test_service_sql_api
│  ├─udf_services
│  ├─version_token
│  └─x
├─router                          MySQL Router
│  ├─cmake
│  ├─doc
│  ├─src
│  └─tests
├─scripts                         SQL批处理, 例如mysqlbug和mysql_install_db
│  ├─systemd
│  └─sys_schema
├─share                           错误消息和字符集
│  └─charsets
├─sql                             处理SQL命令的程序, MySQL的核心.
│  ├─auth
│  ├─conn_handler
│  ├─dd                           数据字典
│  ├─examples
│  ├─gis
│  ├─histograms
│  ├─partitioning
│  ├─regexp                       REGEXP函数实现的正则表达式库
│  ├─resourcegroups
│  └─server_component
├─sql-common                      与sql目录相关的一些实现文件
├─storage                         存储引擎
│  ├─archive
│  ├─blackhole
│  ├─csv
│  ├─example
│  ├─federated
│  ├─heap                           HEAP表处理器
│  ├─innobase                       InnoDB表处理器
│  ├─myisam                         MyISAM表处理器
│  ├─myisammrg                      MyISAM合并表处理器
│  ├─ndb                            MySQL Cluster
│  ├─perfschema
│  ├─secondary_engine_mock
│  └─temptable
├─strings                         C字符串程序库, 例如atof, strchr
│  └─lang_data
├─support-files                   用于在不同系统上构建MySQL的文件
├─testclients                     客户端测试
├─unittest                        单元测试
│  ├─examples
│  ├─gunit
│  └─mytap
├─utilities                       一些工具类
├─vio                             虚拟I/O库
└─win                             为mysql.exe导出符号
```
