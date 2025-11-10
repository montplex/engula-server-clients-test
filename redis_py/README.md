# Test redis-py client with engula-server

redis-py version: v6.4.0

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

## Install python3

```bash
sudo apt-get update
sudo apt-get install -y python3
sudo apt-get install -y python3-invoke
sudo apt-get install -y python3-pip
python3 -V
pip config set global.index-url https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple
```

# Run test cases

## Clone

```bash
git clone https://github.com/redis/redis-py.git
cd redis-py
git checkout v6.4.0
```

## Env preparation

```bash
export PATH=$PATH:$HOME/.local/bin
pip install -r dev_requirements.txt
```

## Run

```bash
docker pull registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17
docker tag registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17 redislabs/client-libs-test:8.0.2
invoke devenv
invoke tests
```

TIPS: If you want to re-run or cleanup, you need to stop and remove the containers first.

```bash
invoke clean
```

## Test for redis 7.2

```bash
docker pull docker.1ms.run/redislabs/client-libs-test:rs-7.2.0-v17
docker tag docker.1ms.run/redislabs/client-libs-test:rs-7.2.0-v17 redislabs/client-libs-test:8.0.2
invoke devenv
invoke tests
```

## View test results

Project redis-py use [pytest](https://docs.pytest.org/en/stable/) test framework, you will see the test detail results by console output.