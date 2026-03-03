[English](./README.md) | [中文](./README_zh.md)

# Engula Server Clients Test

This repository contains test cases for validating engula-server Redis compatibility with various popular Redis client libraries.

## Supported Clients

| Client | Language | Directory |
|--------|----------|-----------|
| redis-py | Python | [redis_py](./redis_py) |
| go-redis | Go | [go_redis](./go_redis) |
| jedis | Java | [jedis](./jedis) |
| lettuce | Java | [lettuce](./lettuce) |
| node-redis | Node.js | [node_redis](./node_redis) |
| NRedisStack | .NET | [n_redis_stack](./n_redis_stack) |

## Quick Start

Each client directory contains its own README with specific instructions. Generally, the workflow involves:

1. Install required dependencies (Docker, language runtime, etc.)
2. Clone the client library repository
3. Run the test suite

Example for redis-py:

```bash
cd redis_py
# Follow instructions in redis_py/README.md
```

## About Engula Server

Engula is a cloud-native, distributed storage engine that aims to be compatible with Redis protocols. For more information, visit [engula.io](https://engula.io).

## License

Each client library follows its own license. This repository is for testing purposes only.
