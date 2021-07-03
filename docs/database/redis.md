### Redis
#### 常用配置设置
```
127.0.0.1:6379> CONFIG GET *
127.0.0.1:6379> config get maxmemory
127.0.0.1:6379> config set maxmemory 4000000000 #4G
```
#### docker快速启动
```bash
#https://hub.docker.com/_/redis/
docker run --name redis -p 6379:6379 -d redis:3.2 redis-server
docker run --name ardb -p 16379:16379 -d lupino/ardb-server
```
#### yum安装
```bash
#Myenv:Amazon Linux AMI release 2017.09
#https://redis.io/download
rpm -ivh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
yum --enablerepo=epel -y install redis
```



---

### Ardb
#### Ardb常用
##### rocksdb参数
> - [https://rocksdb.org.cn/doc/Write-Ahead-Log.html](https://rocksdb.org.cn/doc/Write-Ahead-Log.html)

```
rocksdb.options               write_buffer_size=2048M;max_write_buffer_number=5;min_write_buffer_number_to_merge=2;compression=kSnappyCompression;\
                              bloom_locality=1;memtable_prefix_bloom_size_ratio=0.1;\
                              block_based_table_factory={block_cache=512M;filter_policy=bloomfilter:10:true};\
                              create_if_missing=true;max_open_files=60000;rate_limiter_bytes_per_sec=200M;\
                              use_direct_io_for_flush_and_compaction=true;use_adaptive_mutex=true;max_total_wal_size=20480M
```


