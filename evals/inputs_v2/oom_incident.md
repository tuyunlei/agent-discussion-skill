# 线上问题：订单服务每天凌晨 OOM 重启

## 现象

`order-service`（Java / Spring Boot，跑在 K8s，单 pod limit 2Gi）最近一周，几乎每天凌晨 3:10~3:40 之间被 OOMKilled 一次，自动重启后恢复正常，白天无异常。

## 已知信息

- 容器 `restartCount` 每天 +1，kubectl describe 显示 `Last State: Terminated, Reason: OOMKilled, Exit Code: 137`。
- 凌晨 3:00 有一个定时任务 `DailyReconcileJob`：把前一天的订单全量拉出来对账，写一个汇总表。
- 这个 job 的实现：`List<Order> all = orderRepo.findAll();`（昨天的订单量约 80 万条），然后在内存里 `stream().collect(groupingBy(...))` 做聚合。
- JVM 参数：`-Xmx1500m`，没有配 `-XX:+HeapDumpOnOutOfMemoryError`。
- 监控：pod 内存曲线在 3:05 开始陡升，3:3x 触顶被杀。GC 日志这段时间 Full GC 频繁但回收不下去。
- 最近一次发布在 8 天前，改的是一个跟对账无关的下单接口。

## 我现在的判断

我怀疑是内存泄漏，打算下周排期接 APM、加堆 dump、慢慢抓泄漏点。先记一下，想让另一个 agent 帮我把这个事的根因定位思路捋清楚再动手。
