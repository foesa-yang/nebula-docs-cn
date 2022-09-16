# NebulaGraph 社区版 {{ nebula.release }} release notes

## 缺陷修复

- 修复 LOOKUP 语句中存在 AND 和 IN 运算符导致的崩溃问题。 [#4551](https://github.com/vesoft-inc/nebula/issues/4551)

- 修复`GetProp`中过滤器无效导致的崩溃。[#4568](https://github.com/vesoft-inc/nebula/pull/4568)

- 修复语法错误的表达式导致的崩溃。[#4607](https://github.com/vesoft-inc/nebula/pull/4607)

- 修复多`MATCH`语句相关的并发异常。[#4605](https://github.com/vesoft-inc/nebula/pull/4605)

- 修复扫描顶点或边不过滤过期数据的缺陷。[#4578](https://github.com/vesoft-inc/nebula/pull/4578)

- 修复`properties`函数返回`UNKNOWN PROP`的缺陷。[#4604](https://github.com/vesoft-inc/nebula/pull/4604)

- 修复 raft 中脑裂问题。[#4479](https://github.com/vesoft-inc/nebula/pull/4479)

- 替换过时的 RocksDB API。[#4395](https://github.com/vesoft-inc/nebula/pull/4395)

## 历史版本

[历史版本](https://nebula-graph.com.cn/tags/release-note/)