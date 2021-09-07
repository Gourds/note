
### grafana Query Prometheus

使用query_result结合regex的方法替换原label_values

```bash
#旧写法
label_values(node_uname_info{origin_prometheus=~"$origin_prometheus",job=~"$job",nodename=~"$hostname"},instance)
#支持历史数据，新写法
query_result(count by (instance) (count_over_time(node_uname_info{origin_prometheus=~"$origin_prometheus",job=~"$job",nodename=~"$hostname"}[$__range])))
#Regex： .*instance="(.*)"\}.*
```
