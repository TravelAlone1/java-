[TOC]

# ElasticStack 家族成员及应用场景

## Elastic Stack 生态圈

![](https://note.youdao.com/yws/public/resource/56edfaf3a912c43a05178d48def11ab4/xmlnote/679294C46A254AE38C52EC617DFCE1BA/9627)

## Logstash 特性

- 实时解析和转换数据
  - 从IP地址破译出地理坐标
  - 将PII数据匿名化，完全排除敏感字段
- 可扩展
  - 200多个插件（日志/数据库/Arcsigh/Netflow）

- 可靠性安全性
  - Logstash 会通过持久化队列来保证至少将运行中事件送达一次
  - 数据传输加密
- 监控