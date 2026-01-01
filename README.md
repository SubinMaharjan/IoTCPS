# Instruction to download Tools

Clone the repository

```bash
git clone git@github.com:SubinMaharjan/IoTCPS.git
```

## Installation for FACT

#### Move to FACT directory
```bash
cd FACT
```
#### Build Docker images
```bash
docker build -t fact .
```
#### Start fact container
```bash
docker run -it \
  --name fact-core \
  -p 5001:5000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /Users/subinmaharjan/subin/dockerfiles/data:/home/fact/data \
  --user root \
  fact /bin/bash
```
#### Export docker id
```bash
export DOCKER_ID=$(hostname)
```
#### Check the DOCKER_ID
```bash
echo $DOCKER_ID
```
#### Run installation
```bash
src/install.py
```
#### Start all components
```bash
./start_all_installed_fact_components
```
