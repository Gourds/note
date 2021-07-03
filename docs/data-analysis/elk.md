### 使用建议
> #https://www.elastic.co/cn/blog/how-many-shards-should-i-have-in-my-elasticsearch-cluster
> - 经验法则：确保对于节点上已配置的每个 GB，将分片数量保持在 20 以下。如果某个节点拥有 30GB 的堆内存，那其最多可有 600 个分片，但是在此限值范围内，您设置的分片数量越少，效果就越好。 

> 


> - 避免分片过大，因为这样会对集群从故障中恢复造成不利影响。尽管并没有关于分片大小的固定限值，但是人们通常将 50GB 作为分片上限，而且这一限值在各种用例中都已得到验证。



### 模板设置
```shell
#ES version: 6.5.1
curl  -H "Content-Type: application/json" -XPOST '10.7.0.91:9200/_template/default' -d '
{
  "index_patterns": ["*"],
  "settings": {
    "number_of_shards": 1,
    "number_of_replicas":0
  }
}
'
```
### 
### 内存设置
> - 限制内存使用[【官方文档】](https://www.elastic.co/guide/cn/elasticsearch/guide/current/_limiting_memory_usage.html#monitoring-fielddata) 



**version:**`curl -X GET "localhost:9200/_cluster/health`【6.5.1】


```bash
#https://www.elastic.co/guide/cn/elasticsearch/guide/current/_limiting_memory_usage.html
curl -H "Content-Type: application/json" -XPUT 'localhost:9200/_cluster/settings' -d '
{
  "persistent" : {
    "indices.breaker.fielddata.limit" : "50%" ,
    "indices.breaker.total.limit": "70%"
  }
}
'
```


**获取当前集群配置**


```
curl -XGET 10.5.0.208:9200/_cluster/settings?pretty
```


**设置Fielddata限制**
不能动态调整，可以再配置文件中设置重启生效


```bash
#config/elasticsearch.yml
indices.fielddata.cache.size:  40%
```


查看Fielddata[【api】](https://www.elastic.co/guide/en/elasticsearch/reference/current/indices-stats.html)


```bash
#按索引使用
curl localhost:9200/_stats/fielddata?fields=*\&pretty
#按节点
GET /_nodes/stats/indices/fielddata?fields=*
#按索引节点
GET /_nodes/stats/indices/fielddata?fields=*
```


> **相关参数说明**
> - `indices.fielddata.cache.size`: 控制为 fielddata 分配的堆空间大小。目的是保证安全，而不是内存不足的解决方案



### 参考文档
> - [博客-使用curator管理index](https://arvon.top/2018/08/01/ES%E4%BD%BF%E7%94%A8curator%E7%AE%A1%E7%90%86index/)
> - [博客-Kibana使用sentinl报警实践](https://arvon.top/2018/03/15/Kibana%E4%BD%BF%E7%94%A8sentinl%E6%8A%A5%E8%AD%A6%E5%AE%9E%E8%B7%B5/)



### 常见问题


#### 写es超时


```
org.elasticsearch.cluster.metadata.ProcessClusterEventTimeoutException: failed to process cluster event (put-mapping) within 30s
```


问题可能原因：


- 分片过多，经验值为1个节点最好不要超过1000个分片
- 写入数据自动创建索引容易出现此问题
- 索引的字段数量过多（几十上百个）
- 大批量写入数据refresh时间过短



解决方案：


- 预创建索引
- 减少每个节点分片数
- 集群扩容



可以通过命令查看pending的task


```
curl 10.5.0.208:9200/_cat/pending_tasks?v
insertOrder timeInQueue priority source
       7039        1.7s URGENT   shard-started StartedShardEntry{shardId [[kbilogs-logics-clientbilog-212.2020.10.27][4]], allocationId [aqBWbcs5RhaJfZ9CD9_Kew], message [after existing store recovery; bootstrap_history_uuid=false]}
       7040        1.7s URGENT   shard-started StartedShardEntry{shardId [[kbilogs-logics-multiplayer-211.2020.10.27][0]], allocationId [C8g_qxH0QSSOPElMUk09vw], message [after existing store recovery; bootstrap_history_uuid=false]}
```


