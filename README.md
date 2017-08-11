
# XtraBackup 脚本示例

测试环境 CentOS 7.2 + MySQL 5.7

## 如何安装 XtraBackup 
可以参考 https://www.percona.com/doc/percona-xtrabackup/2.4/installation.html

## 如何使用

### 创建备份帐号 

```
CREATE USER 'xtrabackup'@'localhost' IDENTIFIED BY 'xtra$password';
GRANT SELECT, SHOW VIEW, RELOAD, LOCK TABLES, PROCESS, REPLICATION CLIENT ON *.* TO 'xtrabackup'@'localhost';
FLUSH PRIVILEGES;
```

### 创建备份目录 
在 Linux 系统中, 建立如下文件夹

    |-- /usr/local/xtrabackup/
    |   |-- bash                // 脚本
    |   |-- base                // 全量备份默认位置  
    |   |-- full                // 全量备份和增量备份合并后的备份文件
    |   |-- incremental         // 增量备份默认位置

### 复制备份脚本示例 
然后将项目的内容拷贝到 xtrabackup 文件夹

    |--  /usr/local/xtrabackup/bash
    |-- incremental         // 增量备份默认位置
    |-- incremental         // 增量备份默认位置

### 从模板复制脚本

**以下步骤以全量备份脚本为例**

`cp mysql-base-backup-example mysql-base-backup`

#### 设置执行权限
`chmod a+x mysql-base-backup`

#### 打开脚本设置实际参数
`vi mysql-base-backup` **编辑修改为实际参数**, 

#### 执行脚本
然后执行 `./mysql-base-backup`

如果一切顺利可以看到执行完成的消息

## 设置定时定时执行
执行 `crontab -e`

添加如下内容:  
``` bash
#每个星期日凌晨3:00执行全量备份脚本
0 3 * * 0 /usr/local/xtrabackup/bash/mysql-base-backup. >/dev/null 2>&1
#周一到周六凌晨3:00做增量备份
0 3 * * 1-6 /usr/local/xtrabackup/bash/mysql-incremental-backup >/dev/null 2>&1
#每个星期日凌晨4:00执行清理备份脚本
0 4 * * 0 /usr/local/xtrabackup/bash/mysql-clear-backup. >/dev/null 2>&1
```