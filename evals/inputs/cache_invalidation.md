# 缓存失效方案 (draft)

商品详情页读多写少，我们想在 service 和 DB 之间加一层 Redis 缓存。

## 方案

1. 读：先查 Redis，命中直接返回；未命中查 DB，回填 Redis，TTL 不设（常驻），返回。
2. 写：商品被编辑时，先 `UPDATE` 写 DB，成功后 `DEL` 掉对应的 Redis key。
3. key 用 `product:{id}`，value 是商品 JSON。

## 我们关心的点

- 读路径要快，绝大多数请求走 Redis。
- 商品改了之后，页面要能看到最新的。

我总觉得这个先写库后删缓存的顺序，加上不设 TTL，哪里不太对，但说不清楚。
