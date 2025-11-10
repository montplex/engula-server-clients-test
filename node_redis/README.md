# Test node-redis client with engula-server

node-redis branch: stable

node version: v20.19.5

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

## Install nodejs 20+

```bash
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
node -v
npm -v
```

# Run test cases

## Clone

```bash
git clone https://github.com/redis/node-redis.git
cd node-redis
git checkout stable
```

## Env preparation

```bash
docker pull registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17
docker tag registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17 redislabs/client-libs-test:8.4-RC1-pre.2
```

## Run

```bash
find . -name "*.ts" -type f -exec sed -i 's/8\.2\.1-pre/7.2/g' {} +
npm install
npm run build
npm test -- --redis-version="7.2"
```

## View test results

Project node-redis use [mocha](https://mochajs.org/) test framework, you will see the test detail results by console output.