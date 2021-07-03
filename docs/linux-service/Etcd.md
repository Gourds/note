#### 快速启动
##### Docker单机快速
> 新镜像：gcr.io/etcd-development/etcd

```bash
#v2
export NODE1=10.0.1.42
docker run -d  --name etcd \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=/Users/arvon/Documents/own-project/etcd-data:/etcd-data \
    quay.io/coreos/etcd:v3.3.10 \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data \
    --name node1 \
    --initial-advertise-peer-urls http://${NODE1}:2380 \
    --listen-peer-urls http://0.0.0.0:2380 \
    --advertise-client-urls http://${NODE1}:2379 \
    --listen-client-urls http://0.0.0.0:2379 \
    --initial-cluster node1=http://${NODE1}:2380 \
    --force-new-cluster
 
docker exec etcd etcdctl ls
 
#v3
docker cp snapshot.db etcd:/
docker exec  -e ETCDCTL_API=3 \
	etcd \
    etcdctl snapshot restore snapshot.db \
	--name  node1 \
	--data-dir=/etcd-data/rs \
    --initial-advertise-peer-urls http://${NODE1}:2380 \
    --initial-cluster node1=http://${NODE1}:2380
docker stop etcd
docker run -d --rm --name etcd \
    -p 2379:2379 \
    -p 2380:2380 \
    --volume=/Users/arvon/Documents/own-project/etcd-data:/etcd-data/ \
    quay.io/coreos/etcd:v3.1.20 \
    /usr/local/bin/etcd \
    --data-dir=/etcd-data/rs \
    --name node1 \
    --initial-advertise-peer-urls http://${NODE1}:2380 \
    --listen-peer-urls http://0.0.0.0:2380 \
    --advertise-client-urls http://${NODE1}:2379 \
    --listen-client-urls http://0.0.0.0:2379 \
    --initial-cluster node1=http://${NODE1}:2380 \
    --initial-cluster-state=existing
```
##### docker版集群快速启动
```bash
version: '2'
services:
  etcd0:
    image: quay.io/coreos/etcd
    ports:
      - 2379
    volumes:
      - etcd0:/etcd_data
    command:
      - /usr/local/bin/etcd
      - -name
      - etcd0
      - --data-dir
      - /etcd_data
      - -advertise-client-urls
      - http://etcd0:2379
      - -listen-client-urls
      - http://0.0.0.0:2379
      - -initial-advertise-peer-urls
      - http://etcd0:2380
      - -listen-peer-urls
      - http://0.0.0.0:2380
      - -initial-cluster
      - etcd0=http://etcd0:2380,etcd1=http://etcd1:2380,etcd2=http://etcd2:2380
  etcd1:
    image: quay.io/coreos/etcd
    ports:
      - 2379
    volumes:
      - etcd1:/etcd_data
    command:
      - /usr/local/bin/etcd
      - -name
      - etcd1
      - --data-dir
      - /etcd_data
      - -advertise-client-urls
      - http://etcd1:2379
      - -listen-client-urls
      - http://0.0.0.0:2379
      - -initial-advertise-peer-urls
      - http://etcd1:2380
      - -listen-peer-urls
      - http://0.0.0.0:2380
      - -initial-cluster
      - etcd0=http://etcd0:2380,etcd1=http://etcd1:2380,etcd2=http://etcd2:2380
  etcd2:
    image: quay.io/coreos/etcd
    ports:
      - 2379
    volumes:
      - etcd2:/etcd_data
    command:
      - /usr/local/bin/etcd
      - -name
      - etcd2
      - --data-dir
      - /etcd_data
      - -advertise-client-urls
      - http://etcd2:2379
      - -listen-client-urls
      - http://0.0.0.0:2379
      - -initial-advertise-peer-urls
      - http://etcd2:2380
      - -listen-peer-urls
      - http://0.0.0.0:2380
      - -initial-cluster
      - etcd0=http://etcd0:2380,etcd1=http://etcd1:2380,etcd2=http://etcd2:2380
  etcd-browser:
    build: ./etcd-browser
    ports:
      - 8000
    environment:
      ETCD_HOST: etcd0
      ETCD_PORT: 2379
    depends_on:
      - etcd0
 
volumes:
  etcd0:
  etcd1:
  etcd2:
```
##### etcd可视化工具
```bash
docker run -d --name etcd-view -p 38080:8080 nikfoundas/etcd-viewer 
#printf "taiyouxi:$(openssl passwd -crypt ro3GwH6iTfYc8uFiW)\n" >/usr/local/nginx/taiyouxi_passwd.db
```

---



#### etcd集群还原
> 参考：
> https://www.jianshu.com/p/c60c08effaaa
> https://www.ityoudao.cn/posts/etcd-v2-disaster-recovery/
> 简述：

> - 原启动方式不变，停止所有节点的etcd服务
> - 删除所有节点上etcd的数据目录，即member
> - 下载备份的数据并替换至其中一个节点，即下载数据替换存放于member目录
> - 使用命令行启动此节点
> - 修改更新此节点信息，update
> - 添加一个节点
> - 在添加的节点上执行命令启动
> - 以此类推其他节点

```bash
/usr/local/bin/etcd \
  -name etcd1 \
  --data-dir /opt/etcd/etcd-data \
  -initial-advertise-peer-urls http://10.31.2.65:2380 \
  -listen-peer-urls http://10.31.2.65:2380 \
  -listen-client-urls http://10.31.2.65:2379,http://127.0.0.1:2379 \
  -advertise-client-urls http://10.31.2.65:2379 \
  -initial-cluster-token etcd-cluster-wpys \
  -initial-cluster-state new \
  --force-new-cluster &


etcdctl member list
etcdctl member update 706ad66c870001 http://10.31.2.65:2380
etcdctl member list
etcdctl member add etcd2 http://10.31.2.238:2380


/usr/local/bin/etcd \
  -name etcd2 \
  --data-dir /opt/etcd/etcd-data \
  -initial-advertise-peer-urls http://10.31.2.238:2380 \
  -listen-peer-urls http://10.31.2.238:2380 \
  -listen-client-urls http://10.31.2.238:2379,http://127.0.0.1:2379 \
  -advertise-client-urls http://10.31.2.238:2379 \
  -initial-cluster-token etcd-cluster-wpys \
  -initial-cluster etcd1=http://10.31.2.65:2380,etcd2=http://10.31.2.238:2380 \
  -initial-cluster-state existing &


etcdctl member list
etcdctl member add etcd3 http://10.31.2.205:2380


/usr/local/bin/etcd \
  -name etcd3 \
  --data-dir /opt/etcd/etcd-data \
  -initial-advertise-peer-urls http://10.31.2.205:2380 \
  -listen-peer-urls http://10.31.2.205:2380 \
  -listen-client-urls http://10.31.2.205:2379,http://127.0.0.1:2379 \
  -advertise-client-urls http://10.31.2.205:2379 \
  -initial-cluster-token etcd-cluster-wpys \
  -initial-cluster etcd1=http://10.31.2.65:2380,etcd2=http://10.31.2.238:2380,etcd3=http://10.31.2.205:2380 \
  -initial-cluster-state existing &

## Etcd还原
Etcd地址：http://13.250.157.52:18000/etcd?9
账号密码：taiyouxi/ro3GwH6iTfYc8uFiW
#https://www.jianshu.com/p/c60c08effaaa
#https://www.ityoudao.cn/posts/etcd-v2-disaster-recovery/


/usr/local/bin/etcd \
  -name etcd1 \
  --data-dir /opt/etcd/etcd-data \
  -initial-advertise-peer-urls http://10.31.2.65:2380 \
  -listen-peer-urls http://10.31.2.65:2380 \
  -listen-client-urls http://10.31.2.65:2379,http://127.0.0.1:2379 \
  -advertise-client-urls http://10.31.2.65:2379 \
  -initial-cluster-token etcd-cluster-wpys \
  -initial-cluster-state new \
  --force-new-cluster &


etcdctl member list
etcdctl member update 706ad66c870001 http://10.31.2.65:2380
etcdctl member list
etcdctl member add etcd2 http://10.31.2.238:2380


/usr/local/bin/etcd \
  -name etcd2 \
  --data-dir /opt/etcd/etcd-data \
  -initial-advertise-peer-urls http://10.31.2.238:2380 \
  -listen-peer-urls http://10.31.2.238:2380 \
  -listen-client-urls http://10.31.2.238:2379,http://127.0.0.1:2379 \
  -advertise-client-urls http://10.31.2.238:2379 \
  -initial-cluster-token etcd-cluster-wpys \
  -initial-cluster etcd1=http://10.31.2.65:2380,etcd2=http://10.31.2.238:2380 \
  -initial-cluster-state existing &


etcdctl member list
etcdctl member add etcd3 http://10.31.2.205:2380


/usr/local/bin/etcd \
  -name etcd3 \
  --data-dir /opt/etcd/etcd-data \
  -initial-advertise-peer-urls http://10.31.2.205:2380 \
  -listen-peer-urls http://10.31.2.205:2380 \
  -listen-client-urls http://10.31.2.205:2379,http://127.0.0.1:2379 \
  -advertise-client-urls http://10.31.2.205:2379 \
  -initial-cluster-token etcd-cluster-wpys \
  -initial-cluster etcd1=http://10.31.2.65:2380,etcd2=http://10.31.2.238:2380,etcd3=http://10.31.2.205:2380 \
  -initial-cluster-state existing &
```


