# QuartJob简介

## 1、一句话描述

 Quartz是一个完全由java编写的开源作业调度框架，形式简易，功能强大。 

## 2、核心API(1)、Scheduler

 (1)、Scheduler 

代表一个 Quartz 的独立运行容器，Scheduler 将 Trigger 绑定到特定 JobDetail， 这样当 Trigger 触发时， 对应的 Job 就会被调度。

(2)、Trigger

描述 Job 执行的时间触发规则。主要有 SimpleTrigger 和 CronTrigger 两个子类，通过一个 TriggerKey 唯一标识。

(3)、Job

定义一个任务，规定了任务是执行时的行为。JobExecutionContext 提供了调度器的上下文信息，Job 的数据可从 JobDataMap 中获取。

(4)、JobDetail

Quartz 在每次执行 Job 时，都重新创建一个 Job 实例，所以它不直接接受一个 Job 的实例，相反它接收一个 Job 实现类。描述 Job 的实现类及其它相关的静态信息，如 Job 名字、描述等。

# 二、与SpringBoot2.0 整合


