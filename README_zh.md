[English](./README.md) | [中文](./README_zh.md)

# Engula Server 客户端测试

本项目用于测试 engula-server 与各主流 Redis 客户端库的兼容性。

## 支持的客户端

| 客户端 | 语言 | 目录 |
|--------|------|------|
| redis-py | Python | [redis_py](./redis_py) |
| go-redis | Go | [go_redis](./go_redis) |
| jedis | Java | [jedis](./jedis) |
| lettuce | Java | [lettuce](./lettuce) |
| node-redis | Node.js | [node_redis](./node_redis) |
| NRedisStack | .NET | [n_redis_stack](./n_redis_stack) |

## 快速开始

每个客户端目录下都有对应的 README 文件，包含具体的测试步骤。一般流程如下：

1. 安装依赖（Docker、运行时等）
2. 克隆客户端库仓库
3. 运行测试套件

以 redis-py 为例：

```bash
cd redis_py
# 按照 redis_py/README.md 中的说明操作
```

## 关于 Engula Server

Engula 是一个云原生的分布式存储引擎，旨在与 Redis 协议兼容。更多信息请访问 [engula.io](https://engula.io)。

## 许可证

各客户端库遵循各自的许可证。本项目仅用于测试目的。
