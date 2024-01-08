---
title: Fix Redis After Reboot on Ubuntu
description: Rescue your Laravel app from Redis connection issues after server reboot on Ubuntu
publishDate: 2024-01-08
tags: [redis, laravel, ubuntu]
---

After my server rebooted, my Laravel app suddenly crashed, and upon checking the logs, I discovered that the app couldn't connect to the Redis server.

```bash
production.ERROR: Connection refused [tcp://127.0.0.1:6379]
[object] (Predis\\Connection\\ConnectionException(code: 111)
```

It's worth noting that the app was installed on a bare-metal server, not a Docker container, and Redis was installed using `apt install redis`.

To confirm Redis installation, I ran `apt-cache policy redis`:

```bash
redis:
  Installed: 6:7.2.3-1rl1~bionic1
  Candidate: 6:7.2.3-1rl1~bionic1

```

As seen, Redis is installed. However, running `systemctl status redis` returned:

```bash
Unit redis.service could not be found.
```

After researching, I found this [post](https://stackoverflow.com/a/57694156/302974) that mentioned:

> The problem is that symlink redis-server.service to redis.service was deleted.

So the solution is running the command below:

```bash
$ systemctl enable redis-server
Synchronizing state of redis-server.service with SysV service script with /lib/systemd/systemd-sysv-install.
Executing: /lib/systemd/systemd-sysv-install enable redis-server
Created symlink /etc/systemd/system/redis.service → /lib/systemd/system/redis-server.service.
```

This created a symlink. Despite this, Redis still didn't start:

```bash
$ service redis status
● redis-server.service - Advanced key-value store
   Loaded: loaded (/lib/systemd/system/redis-server.service; enabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: http://redis.io/documentation,
           man:redis-server(1)
```

To resolve this, a simple start was needed:

```bash
$ service redis start
```

After restarting, checking the status showed that everything was running smoothly:

```bash
$ service redis status

● redis-server.service - Advanced key-value store
   Loaded: loaded (/lib/systemd/system/redis-server.service; enabled; vendor preset: enabled)
   Active: active (running) since Mon 2024-01-08 12:02:13 WIB; 1s ago
     Docs: http://redis.io/documentation,
           man:redis-server(1)
 Main PID: 29254 (redis-server)
   Status: "Ready to accept connections"
    Tasks: 6 (limit: 4654)
   CGroup: /system.slice/redis-server.service
           └─29254 /usr/bin/redis-server 127.0.0.1:6379
```
