---
title: Safely Delete MySQL Bin Logs
description: Learn to troubleshoot and prevent database server disk space issues by managing MySQL binary logs efficiently.
publishDate: 2023-12-28
tags: [mysql]
---

Have you ever encountered a situation where your database server runs out of disk space, even when the database itself is relatively small? The culprit might be the binary logs.

To assess the disk usage of the logs, you can use the following command:

```sh
du -h -d 1 /var/log/mysql
27.1G	/var/log/mysql
```

If you find yourself in this scenario, a straightforward solution is to execute the following MySQL command:

```sql
PURGE BINARY LOGS BEFORE NOW();

-- or using interval. ex 3 days
PURGE BINARY LOGS BEFORE NOW() - INTERVAL 3 DAY;
```

To proactively manage disk space and avoid future log-related issues, consider adjusting the `/etc/mysql/my.cnf` configuration file:

```ini
[mysqld]

# others config

expire_logs_days = 3
```

This setting ensures the automatic deletion of logs older than 3 days. Remember to restart your MySQL service with service mysql restart to apply these changes effectively.
