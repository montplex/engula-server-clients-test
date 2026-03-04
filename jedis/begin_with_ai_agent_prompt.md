# Jedis单元测试 - Redis v6与v7对比测试指南

## 需求描述

测试jedis的一个具体版本的unit tests，针对redis v6和v7两种情况的测试结果并作对比。本文档以user = kerry为例进行测试，相对应的目录AI agent自己需要调整。

## 环境信息

- **JDK版本**: openjdk-17 (21 Jedis部分ByteBuddy不兼容，不能用21测试)
- **Jedis版本**: v6.2.0
- **Redis v6源码**: `/home/kerry/ws/redis_v6` (已构建, 版本: 6.2.18)
- **Redis v7源码**: `/home/kerry/ws/redis` (已构建, 版本: 7.2.11)
- **测试文档参考**: `/home/kerry/ws/engula-server-clients-test/jedis/README.md`

## 关键要求

1. 忽略TLS相关测试case
2. 在`make test`中mvn test之前需要添加一个target来验证prepared started redis-server的版本

## 实现步骤

### 1. 克隆Jedis仓库

```bash
cd /home/kerry/ws
git clone https://github.com/redis/jedis.git
cd jedis
git checkout v6.2.0
```

### 2. 修改Makefile

需要做以下修改：

1. 添加`verify-redis-version` target：在mvn test之前验证redis-server版本
2. 移除所有TLS相关配置（tls-port, tls-cert-file, tls-key-file, tls-ca-cert-file, tls-auth-clients）
3. 移除`enable-module-command`选项（Redis v6不支持）

修改后的Makefile关键部分：

```makefile
test: | start verify-redis-version mvn-test stop

verify-redis-version:
	@echo "=============================================="
	@echo "Verifying redis-server version..."
	@REDIS_VERSION=$$(redis-server -v 2>&1 | grep -oP 'v=\K[0-9]+\.[0-9]+\.[0-9]+'); \
	echo "Detected redis-server version: $$REDIS_VERSION"; \
	if [ -z "$$REDIS_VERSION" ]; then \
		echo "Error: Could not detect redis-server version"; \
		exit 1; \
	fi; \
	echo "=============================================="

mvn-test:
	MAVEN_OPTS="--add-opens=java.base/java.io=ALL-UNNAMED" mvn -Dtest=${TEST} -Dexclude="**/SSL*Test.java" clean compile test
```

### 3. 启动Redis服务

**Redis v6测试：**
```bash
cd /home/kerry/ws/jedis
PATH=/home/kerry/ws/redis_v6/src:$PATH make start
```

**Redis v7测试：**
```bash
cd /home/kerry/ws/jedis
PATH=/home/kerry/ws/redis/src:$PATH make start
```

注意：对于Redis v7，cluster创建可能需要手动执行：
```bash
/home/kerry/ws/redis/src/redis-cli -a cluster --cluster create 127.0.0.1:7479 127.0.0.1:7480 127.0.0.1:7481 --cluster-yes
docker run -p 6479:6379 --name jedis-stack -e PORT=6379 -d redislabs/client-libs-test:8.2
```

### 4. 运行测试

**Redis v6：**
```bash
cd /home/kerry/ws/jedis
PATH=/home/kerry/ws/redis_v6/src:$PATH make verify-redis-version
TEST=JedisTest make mvn-test
TEST=ConnectionTest make mvn-test
```

**Redis v7：**
```bash
cd /home/kerry/ws/jedis
PATH=/home/kerry/ws/redis/src:$PATH make verify-redis-version
TEST=JedisTest make mvn-test
TEST=ConnectionTest make mvn-test
```

### 5. 停止服务

```bash
cd /home/kerry/ws/jedis
make stop
```

## 测试结果对比

| 测试用例 | Redis v6.2.18 | Redis v7.2.11 |
|---------|--------------|---------------|
| JedisTest | 71 tests, 2 skipped | 75 tests, 0 skipped |
| ConnectionTest | 5 tests, 0 skipped | 5 tests, 0 skipped |

## 然后提示AI agent

开始对v6进行所有的unit tests测试并记录log，然后v7，然后对比两个结果log并作对比总结到summary.html

## 注意事项

1. 测试完成后记得执行`make stop`停止所有Redis实例
2. 如果遇到"Node is not empty"错误，需要先清理Redis数据或重启
3. 某些测试在Redis v6上可能有skip，这是正常的兼容性问题
