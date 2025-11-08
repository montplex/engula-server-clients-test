# Test go-redis client with engula-server

go-redis version: v9.3.1

go version: 1.24.6

# Prepare

## Install go 1.24

```bash
wget https://dl.google.com/go/go1.24.6.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.24.6.linux-amd64.tar.gz
sudo ln -s /usr/local/go/bin/go /usr/bin/go
go version
go env -w GO111MODULE=on && go env -w GOPROXY=https://goproxy.cn,direct
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

# Run test cases

## Clone

```bash
git clone https://github.com/redis/go-redis.git
cd go-redis
git checkout v9.3.1
```

## Change config

Update redis-server bin copy in Makefile. Change target testdeps:

```makefile
testdeps:
	mkdir -p ./testdata/redis/src
	cp /usr/local/bin/engula-server ./testdata/redis/src/redis-server
```

## Run

```bash
REDIS_PORT=6379 make test
```

TIPS: ignore failed cases for redis module FT/JSON/TS

## View test results

Project go-redis use [ginkgo](https://onsi.github.io/ginkgo/) test framework, you will see the test result by console output.