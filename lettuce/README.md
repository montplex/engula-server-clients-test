# Test lettuce client with engula-server

Lettuce version: 6.3.0

OpenJDK version: 17.0.2

# Prepare

## Install openjdk 17 and maven 3.9.x

```bash
sudo apt-get update
sudo apt-get install openjdk-17-jdk
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk-amd64
export PATH=$PATH:$JAVA_HOME/bin
java -version
wget https://dlcdn.apache.org/maven/maven-3/3.9.11/binaries/apache-maven-3.9.11-bin.tar.gz
tar -zxvf apache-maven-3.9.11-bin.tar.gz
export MAVEN_HOME=$(pwd)/apache-maven-3.9.11
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
  <id>aliyunmaven</id>
  <mirrorOf>*</mirrorOf>
  <name>阿里云公共仓库</name>
  <url>https://maven.aliyun.com/repository/public</url>
</mirror>
  </mirrors>
</settings>
```

## Install stunnel 

```bash
sudo apt-get install stunnel4
```

Make sure port 6443 is not used. As stunnel will bind to port 6443.

## Copy bin files

```bash
sudo cp engula-server /usr/local/bin/redis-server
sudo cp redis-cli /usr/local/bin/redis-cli
redis-server -v
```

TIPS: Need BUILD_TLS=yes when build redis-server or engula-server. 

### Build engula-server / redis-server with TLS

```bash
sudo apt-get install -y libssl-dev
cd redis
git checkout 7.2.4
make distclean && make BUILD_TLS=yes -j4
```

# Run test cases

## Clone

```bash
git clone https://github.com/redis/lettuce.git
cd lettuce
git checkout 6.3.0.RELEASE
```

## Change Makefile

Copy bin files instead do git clone and compile. Change target:

```makefile
work/redis-git/src/redis-cli work/redis-git/src/redis-server:
	[ -d "work/redis-git" ] && cd work/redis-git && git reset --hard || \
	mkdir -p work/redis-git/src && cp /usr/local/bin/redis-cli work/redis-git/src/redis-cli && cp /usr/local/bin/engula-server work/redis-git/src/redis-server
```

Make sure you have copied bin files /usr/local/bin/redis-cli and /usr/local/bin/engula-server or /usr/local/bin/redis-server
If you want to test redis-server, change 'cp /usr/local/bin/engula-server work/redis-git/src/redis-server' to 'cp /usr/local/bin/redis-server work/redis-git/src/redis-server'

## Change some blocking cases

vi lettuce/src/test/java/io/lettuce/core/commands/reactive/TransactionReactiveCommandIntegrationTests.java
Add skip annotation to method `watchRollback`, need add import `import org.junit.jupiter.api.Disabled;` on top of class.

```java
    @Test
    @Disabled
    void watchRollback() {
    }
```

## Run

```bash
make test
```

## Cleanup

```bash
make stop
make cleanup
```

## Rerun

Need remove dir work

```bash
rm -rf work
make test
```

## View test results

Project lettuce use [junit 5](https://junit.org/) test framework, you will see the test detail results by console output.