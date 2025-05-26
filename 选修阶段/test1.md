```bash
sed -rn '75p;94p;257p;289p;302p;454p;901p' conf/redis.conf
bind 0.0.0.0
protected-mode no
daemonize yes
pidfile /usr/local/redis/logs/redis.pid
logfile "/usr/local/redis/logs/redis.log"
dir /usr/local/redis/data/
requirepass 123456
```





```bash
yum -y install python3-devel mysql-devel gcc make cairo-devel gobject-introspection-devel cairo-gobject dbus-devel dbus-glib-devel


python -m pip install -r requirement.txt -i https://pypi.tuna.tsinghua.edu.cn/simple/

```





```bash
ERROR: Could not find a version that satisfies the requirement libcomps==0.1.18 (from versions: 0.1.20.post1, 0.1.21.post1)
ERROR: No matching distribution found for libcomps==0.1.18


ERROR: Could not find a version that satisfies the requirement perf==0.1 (from versions: none)
ERROR: No matching distribution found for perf==0.1


ERROR: Could not find a version that satisfies the requirement python-linux-procfs==0.7.0 (from versions: none)
ERROR: No matching distribution found for python-linux-procfs==0.7.0

ERROR: Could not find a version that satisfies the requirement rpm==4.14.3 (from versions: 0.0.1, 0.0.2, 0.0.3.1, 0.0.3.2, 0.1.0, 0.2.0, 0.3.0, 0.3.1, 0.4.0)
ERROR: No matching distribution found for rpm==4.14.3
```

