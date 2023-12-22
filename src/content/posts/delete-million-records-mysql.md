---
title: Deleting Millions of MySQL Records
description: Discover an uncomplicated yet powerful technique for deleting a substantial volume of records in MySQL, along with essential post-deletion steps.
publishDate: 2023-12-22
tags: [mysql, laravel]
---

I have implemented custom middleware to log all requests and responses in my Laravel application. Over time, the recorded data has surpassed 100 million entries, resulting in a significantly large database size.

Upon researching solutions, I discovered various methods to efficiently delete millions of records from a MySQL database. Among them, I found the following approach to be the most straightforward:

```sql
DELETE FROM `table`
WHERE `created_at` < NOW() - INTERVAL 14 DAYS
ORDER BY `id`
LIMIT 100000
```

Feel free to customize the column settings for date, order (the table's primary key), and the interval according to your preferences.

When using DELETE with LIMIT, it's a good idea to add ORDER BY. Skipping this step might lead to unexpected problems, like breaking replication in some cases.

Simply repeat this process until no rows are affected.

Once the cleanup is completed, it is essential to execute the following command to reclaim disk space and reduce the table size:

```sql
OPTIMIZE TABLE `table`
```

This optimization step ensures that the table is efficiently organized and that disk usage is minimized.
