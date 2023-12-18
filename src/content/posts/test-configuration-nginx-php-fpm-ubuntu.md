---
title: "How to Test and Restart Nginx and PHP-FPM Services on Ubuntu"
description: "Learn how to check the configuration files of Nginx and PHP-FPM for errors and restart the services safely on Ubuntu."
publishDate: 2023-12-11
tags: [short, nginx, php-fpm, ubuntu]
---

Before applying any changes to the configuration files of Nginx or PHP-FPM, it is advisable to test them for any errors to avoid breaking the server.

## Nginx

```bash
nginx -t
```

The expected output should look like this

```bash
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
```

If the test is successful, you can safely restart the service with `service nginx restart`

## PHP-FPM

```bash
php-fpm{version} -t # ex: php-fpm8.2 -t
```

The expected output should look like this

```bash
NOTICE: configuration file /etc/php/8.2/fpm/php-fpm.conf test is successful
```

Similarly, if the test is successful, you can restart the service with `service php{version}-fpm restart`, ex `service php7.4-fpm restart`
