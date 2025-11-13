# Test NRedisStack client with engula-server

NRedisStack version: v1.1.1

Dotnet version: 9.0

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

## Install dotnet 9.0

```bash
wget https://packages.microsoft.com/config/ubuntu/22.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb 
sudo dpkg -i packages-microsoft-prod.deb 
rm packages-microsoft-prod.deb 
sudo apt-get update 
sudo apt-get install -y dotnet-sdk-9.0 
dotnet --version
```

# Run test cases

## Clone

```bash
git clone https://github.com/redis/NRedisStack.git
cd NRedisStack
git checkout v1.1.1
```

## Env preparation

```bash
docker pull registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17
docker tag registry.cn-guangzhou.aliyuncs.com/montplex/client-libs-test:rs-7.2.0-v17 redislabs/client-libs-test:7.2
CLIENT_LIBS_TEST_IMAGE=redislabs/client-libs-test:7.2 docker-compose -f tests/dockers/docker-compose.yml --profile all up -d
```

## Run

```bash
dotnet restore
dotnet build
REDIS_VERSION=7.2.4 dotnet test --framework net9.0
```

## Clean up

```bash
docker-compose -f tests/dockers/docker-compose.yml --profile all down
```

## Test for redis 7.2

```bash
docker pull docker.1ms.run/redislabs/client-libs-test:rs-7.2.0-v17
docker tag docker.1ms.run/redislabs/client-libs-test:rs-7.2.0-v17 redislabs/client-libs-test:7.2
docker-compose -f tests/dockers/docker-compose.yml --profile all down
CLIENT_LIBS_TEST_IMAGE=redislabs/client-libs-test:7.2 docker-compose -f tests/dockers/docker-compose.yml --profile all up -d
REDIS_VERSION=7.2.4 dotnet test --framework net9.0
```