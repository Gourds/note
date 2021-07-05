### Install
```bash
docker run \
  --volume=/:/rootfs:ro \
  --volume=/var/run:/var/run:rw \
  --volume=/sys:/sys:ro \
  --volume=/opt/docker/:/var/lib/docker:ro \
  --privileged=true \
  --publish=12306:8080 \
  --detach=true \
  --name=cadvisor \
  google/cadvisor:canary
```

### 可能存在的问题

>Failed to start container manager: inotify_add_watch /sys/fs/cgroup/cpuacct,cpu: no such file or directory

**Tips**
以上启动参数可以修改`detach=false`来看完整log

解决：
```bash
ll -d /sys/fs/cgroup/cpu*
mount -o remount,rw '/sys/fs/cgroup'
ln -s /sys/fs/cgroup/cpu,cpuacct /sys/fs/cgroup/cpuacct,cpu
```
