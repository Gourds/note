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

#### 可能存在的问题

>Failed to start container manager: inotify_add_watch /sys/fs/cgroup/cpuacct,cpu: no such file or directory

**Tips**
以上启动参数可以修改`detach=false`来看完整log

解决：
```bash
ll -d /sys/fs/cgroup/cpu*
mount -o remount,rw '/sys/fs/cgroup'
ln -s /sys/fs/cgroup/cpu,cpuacct /sys/fs/cgroup/cpuacct,cpu
```

### 常见的指标


```xml
container_cpu_load_average_10s gauge 过去10秒容器CPU的平均负载
container_cpu_usage_seconds_total counter 容器在每个CPU内核上的累积占用时间 (单位：秒)
container_cpu_system_seconds_total counter System CPU累积占用时间（单位：秒）
container_cpu_user_seconds_total counter User CPU累积占用时间（单位：秒）
container_fs_usage_bytes gauge 容器中文件系统的使用量(单位：字节)
container_fs_limit_bytes gauge 容器可以使用的文件系统总量(单位：字节)
container_fs_reads_bytes_total counter 容器累积读取数据的总量(单位：字节)
container_fs_writes_bytes_total counter 容器累积写入数据的总量(单位：字节)
container_memory_max_usage_bytes gauge 容器的最大内存使用量（单位：字节）
container_memory_usage_bytes gauge 容器当前的内存使用量（单位：字节
container_spec_memory_limit_bytes gauge 容器的内存使用量限制
machine_memory_bytes gauge 当前主机的内存总量
container_network_receive_bytes_total counter 容器网络累积接收数据总量（单位：字节）
container_network_transmit_bytes_total counter 容器网络累积传输数据总量（单位：字节）
container_last_seen{name=~".+"}  上次检测到容器应用的时间
```

**Tips:**
- `container_last_seen`: 可用来检测容器中应用的运行状态（running/exit）,有值即应用OK，无值则应用挂了
