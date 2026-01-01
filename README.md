# Instruction to download Tools

Clone the repository

```bash
git clone git@github.com:SubinMaharjan/IoTCPS.git
```

## Installation for FACT

#### 1. Move to FACT directory
```bash
cd FACT
```
#### 2. Build Docker images
```bash
docker build -t fact .
```
##### OR, Alternatively download the images
```bash
docker pull mhrznsubin/fact:0.1
```
#### 3. Start fact container
```bash
docker run -it \
  --name fact-core \
  -p 5001:5000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v path_to_FACT_dir/data:/home/fact/data \
  --user root \
  fact /bin/bash
```
##### If you downloaded the image, start fact container using image name
```bash
docker run -it \
  --name fact-core \
  -p 5001:5000 \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v path_to_FACT_dir/data:/home/fact/data \
  --user root \
  mhrznsubin/fact:0.1 /bin/bash
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
### May ask for postgress password
```bash
Password: postgrespassword
```
#### Start all components
```bash
./start_all_installed_fact_components
```
### Open FACT application
```bash
http://localhost:5001
```