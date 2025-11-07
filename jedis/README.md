# Test jedis client with engula-server

Jedis version: v6.2.0

Jedis supported Redis versions, [detail](https://github.com/redis/jedis)

# Prepare

## Install docker-ce

For Ubuntu 22.04 as an example:

```bash
sudo apt-get update
sudo apt-get upgrade
sudo apt-get install ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker ${USER}
newgrp docker
sudo systemctl restart docker
sudo systemctl enable docker
docker ps
```

## Install libssl

```bash
wget http://security.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1-1ubuntu2.1~18.04.23_amd64.deb
sudo dpkg -i libssl1.1_1.1.1-1ubuntu2.1~18.04.23_amd64.deb
```

## Install openjdk 17 and maven 3.9.x

```bash
sudo apt-get update
sudo apt-get install openjdk-17-jdk
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
java -version
wget https://dlcdn.apache.org/maven/maven-3/3.9.11/binaries/apache-maven-3.9.11-bin.tar.gz
tar -zxvf apache-maven-3.9.11-bin.tar.gz
export MAVEN_HOME=/home/$(whoami)/apache-maven-3.9.11
export PATH=$PATH:$MAVEN_HOME/bin
mvn -v
```

## Update maven repository mirror (Optional)

Update file ~/.m2/settings.xml

```text
<?xml version="1.0" encoding="UTF-8"?>
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0 http://maven.apache.org/xsd/settings-1.0.0.xsd">
<mirrors>
	<mirror>
	  <id>alimaven</id>
	  <name>aliyun maven</name>
	  <url>https://maven.aliyun.com/repository/public</url>
	  <mirrorOf>central</mirrorOf>
	</mirror>
  </mirrors>
</settings>
```

## Download engula-server release for linux amd64.

[engula-server release](https://engula.io/docs/getting-started/download)

## Untar and copy bin files

```bash
tar zxvf engula2.1.3.linux-amd64.tar.gz
sudo cp bin/redis-server /usr/local/bin/redis-server
sudo cp bin/redis-cli /usr/local/bin/redis-cli
redis-server -v
redis-cli -v
```

# Run test cases for engula-server 2.x

## Clone

```bash
git clone https://github.com/redis/jedis.git
cd jedis
git checkout v6.2.0
```

## Run

```bash
docker pull registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17
docker tag registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17 redislabs/client-libs-test:8.2
TEST_ENV_PROVIDER=not_in_docker make test
```

TIPS: If you want to re-run or cleanup, need kill the processes first.

```bash
sudo pkill redis-server
docker stop jedis-stack
docker rm jedis-stack
rm /tmp/redis*
```

## View test results

Project jedis use [junit 5](https://junit.org/) test framework, you will see the test detail results by console output.

# Run test cases for redis-server 7.2

## Build redis-server 7.2

```bash
git clone https://github.com/redis/redis.git
cd redis
git checkout 7.2.4
make distclean
make BUILD_TLS=yes -j8
```

## Copy bin files

```bash
sudo cp src/redis-server /usr/local/bin/redis-server
sudo cp src/redis-cli /usr/local/bin/redis-cli
redis-server -v
redis-cli -v
```

## Use back docker image

```bash
docker rmi redislabs/client-libs-test:8.2
docker pull redislabs/client-libs-test:8.2
```

## Run

```bash
TEST_ENV_PROVIDER=not_in_docker make test
```