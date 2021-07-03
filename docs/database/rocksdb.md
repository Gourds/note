### Ardb的调优建议
> 基于 RocksDB 设计存储系统，要考虑到应用场景进行各种 tradeoff 设置相关参数。譬如，如果 RocksDB 进行 compaction 比较频繁，虽然有利于空间和读，但是会造成读放大；compaction 过低则会造成读放大和空间放大；增大每个 level 的 comparession 难度可以减小空间放大，但是会增加 cpu 负担，是运算时间增加换取使用空间减小；增大 SSTfile 的 data block size，则是增大内存使用量来加快读取数据的速度，减小读放大。




---

### 目前使用Ardb的参数说明
```bash
write_buffer_size=2048M;\
max_write_buffer_number=5;\
min_write_buffer_number_to_merge=2;\
compression=kSnappyCompression;\
bloom_locality=1;\
memtable_prefix_bloom_size_ratio=0.1;\
block_based_table_factory={block_cache=512M;filter_policy=bloomfilter:10:true};\
create_if_missing=true;\
max_open_files=60000;\
rate_limiter_bytes_per_sec=200M;\
use_direct_io_for_flush_and_compaction=true;\
use_adaptive_mutex=true;\
max_total_wal_size=20480M
```
> - `write_buffer_size` 指定了一个写内存 buffer 的大小，当这个 buffer 写满之后数据会被固化到磁盘上。这个值越大批量写入的性能越好。
> - `max_write_buffer_number` RocksDB 控制写内存 buffer 数目的参数，这个值默认是 2，当一个 buffer 的数据被 flush 到磁盘上的时候，RocksDB 就用另一个 buffer 作为数据读写缓冲区。
> - `min_write_buffer_number_to_merge` 设定了把写 buffer 的数据固化到磁盘上时对多少个 buffer 的数据进行合并然后再固化到磁盘上。这个值如果为 1，则 L0 层文件只有一个，这会导致读放大，这个值太小会导致数据固化到磁盘上之前数据去重效果太差劲。

**调优参数尝试** 
```bash
#blockCacheSize=1350 * 1024 * 1024 #块缓存大小 建议预算内存的1/3，
options.compaction_style = kCompactionStyleLevel;
write_buffer_size = 67108864; // 64MB
max_write_buffer_number = 3;
target_file_size_base = 67108864; // 64MB
max_background_compactions = 4;
level0_file_num_compaction_trigger = 8;
level0_slowdown_writes_trigger = 17;
level0_stop_writes_trigger = 24;
num_levels = 4;
max_bytes_for_level_base = 536870912; // 512MB
max_bytes_for_level_multiplier = 8;
bloom_locality = 1; #调整 bloom filter 以减少内存访问
```
> 我们使用高并发性的级别样式压缩。内存表大小为64mb，0级文件的总数为8个。这意味着当 L0大小增长到512 MB 时，就会触发压缩。L1大小为512 MB，每个级别比前一级大8倍。L2是4gb，L3是32gb。 




---

### 推荐配置参数
#### 固态flash
```xml
options.options.compaction_style = kCompactionStyleLevel;
options.write_buffer_size = 67108864; // 64MB
options.max_write_buffer_number = 3;
options.target_file_size_base = 67108864; // 64MB
options.max_background_compactions = 4;
options.level0_file_num_compaction_trigger = 8;
options.level0_slowdown_writes_trigger = 17;
options.level0_stop_writes_trigger = 24;
options.num_levels = 4;
options.max_bytes_for_level_base = 536870912; // 512MB
options.max_bytes_for_level_multiplier = 8;
```
#### 全内存
```xml
options.allow_mmap_reads = true;
BlockBasedTableOptions table_options;
table_options.filter_policy.reset(NewBloomFilterPolicy(10, true));
table_options.no_block_cache = true;
table_options.block_restart_interval = 4;
options.table_factory.reset(NewBlockBasedTableFactory(table_options));
options.level0_file_num_compaction_trigger = 1;
options.max_background_flushes = 8;
options.max_background_compactions = 8;
options.max_subcompactions = 4;
options.max_open_files = -1;
ReadOptions.verify_checksums = false
```

---

### RocksDB的原理
#### RocksDB的Memory
> ##### 1. Block Cache
> - 存储一些读缓存数据，它的下一层是操作系统的 Page Cache
> ##### 2. Indexes and bloom filters
> - Index 由 key、offset 和 size 三部分构成，当 Block Cache 增大 Block Size 时，block 个数必会减小，index 个数也会随之降低，如果减小 key size，index 占用内存空间的量也会随之降低。
> - filter是 bloom filter 的实现，如果假阳率是 1%，每个key占用 10 bits，则总占用空间就是 `num_of_keys * 10 bits`，如果缩小 bloom 占用的空间，可以设置 `options.optimize_filters_for_hits = true`，则最后一个 level 的 filter 会被关闭，bloom 占用率只会用到原来的 10% 。
> - 结合 block cache 所述，index & filter 有如下优化选项：
>    - `cache_index_and_filter_blocks` 这个 option 如果为 true，则 index & filter 会被存入 block cache，而 block cache 中的内容会随着 page cache 被交换到磁盘上，这就会大大降低 RocksDB的性能，把这个 option 设为 true 的同时也把 `pin_l0_filter_and_index_blocks_in_cache` 设为 true，以减小对性能的影响。
> - 如果 `cache_index_and_filter_blocks` 被设置为 false （其值默认就是 false），index/filter 个数就会受 `max_open_files` 影响，官方建议把这个选项设置为 -1，以方便 RocksDB 加载所有的 index 和 filter 文件，最大化程序性能。
> ##### 3. Memtables
> - block cache、index & filter 都是读 buffer，而 memtable 则是写 buffer，所有 kv 首先都会被写进 memtable，其 size 是 `write_buffer_size`。 memtable 占用的空间越大，则写放大效应越小，因为数据在内存被整理好，磁盘上就越少的内容会被 compaction。如果 memtable 磁盘空间增大，则 L1 size 也就随之增大，L1 空间大小受 `max_bytes_for_level_base` option 控制。
> ##### 4. Blocked pinned by iterators
> - 这部分内存空间一般占用总量不多，但是如果有 100k 之多的transactions 发生，每个 iterator 与一个 data block 外加一个 L1 的 data block，所以内存使用量大约为 `num_iterators * block_size * ((num_levels-1) + num_l0_files)`。




---

### 参考文档
> - [http://alexstocks.github.io/html/rocksdb.html](http://alexstocks.github.io/html/rocksdb.html)
> - [https://github.com/facebook/rocksdb/wiki/Setup-Options-and-Basic-Tuning#block-cache-size](https://github.com/facebook/rocksdb/wiki/Setup-Options-and-Basic-Tuning#block-cache-size)
> - [https://www.dazhuanlan.com/2019/09/28/5d8f695e6c7bb/](https://www.dazhuanlan.com/2019/09/28/5d8f695e6c7bb/)
> - [https://github.com/facebook/rocksdb/wiki/RocksDB-Tuning-Guide](https://github.com/facebook/rocksdb/wiki/RocksDB-Tuning-Guide)



