# springboot 整合 elasticsearch

## 简介

我们的应用经常需要添加检索功能，开源的 ElasticSearch 是目前全文搜索引擎的 首选。他可以快速的存储、搜索和分析海量数据。Spring Boot通过整合Spring Data ElasticSearch为我们提供了非常便捷的检索功能支持;
 Elasticsearch是一个分布式搜索服务，提供Restful API，底层基于Lucene，采用 多shard(分片)的方式保证数据安全，并且提供自动resharding的功能，github 等大型的站点也是采用了ElasticSearch作为其搜索服务。

## 版本对应关系

spring data elasticsearch	elasticsearch
3.2.x	6.5.0
3.1.x	6.2.2
3.0.x	5.5.0
2.1.x	2.4.0
2.0.x	2.2.0
1.3.x	1.5.2

