---
title: checkpoint的生产配置
catalog: true
comments: true
indexing: true
header-img: ../../../../img/default.jpg
top: false
tocnum: true
date: 2022-10-09 15:34:17
subtitle:
tags: Flink
categories: Flink
---

## Checkpoint相关配置





| 配置项                                                       | 含义                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| state.backend                                                | 状态存储后端，可配置值有'jobmanager', 'filesystem', 'rocksdb'等，在checkpoint数据存储在hdfs上时，配置该值为filesystem |
| state.checkpoints.dir                                        | checkpoints写出的目录位置，比如：hdfs://ns/flink/flink-checkpoints |
| state.savepoints.dir                                         | savepoints写出的目录位置，比如：hdfs://ns/flink/flink-checkpoints，一般与上面的值配置一致 |
| execution.checkpointing.mode                                 | 检查点模式，默认值EXACTLY_ONCE，可选项AT_LEAST_ONCE          |
| execution.checkpointing.interval                             | checkpoint的时间间隔，单位毫秒，比如5000                     |
| state.checkpoints.num-retained                               | checkpoint保留的个数，默认值1，不过建议比如设置为3，防止想要恢复更久前的状态，或者最近的一个checkpoint被误删除等情况。 |
| execution.checkpointing.externalized-checkpoint-retention    | 该配置项定义了在任务取消（cancel，注意不是job failed是主动的cancel）时如何清理外部化的检查点。一般我们会配置为RETAIN_ON_CANCELLATION，即cancel时保留检查点。而DELETE_ON_CANCELLATION则表示cancel任务时删除检查点，只有在任务失败时，才会被保留。 |
| <font color=red>execution.checkpointing.tolerable-failed-checkpoints</font> | 允许的连续checkpoint失败的次数，默认值是0，即只要出现checkpoint失败，作业就会失败，然后按照设置了的重启策略开始重启（setRestartStrategy），所以生产环境中一般设置2，即允许偶尔出现checkpoints失败的。 |


 <font color=red>execution.checkpointing.tolerable-failed-checkpoints</font> 需要特别注意，flink可能会因为某些原因被hang住，导致cp失败，如果不设置，容忍就是0，则会直接导致任务挂掉。严重可能导致任务连续失败。所以一定要根据任务重要程度设置一定的容忍度。
