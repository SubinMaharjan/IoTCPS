# Installation for FACT

### 1. Move to Firmadyne directory
```bash
cd Firmadyne
```

### 2. Build the docker image
```bash
build -t firmadyne .
```

### Alternatively, pull the image and run
```bash
docker pull mhrznsubin/firmadyne:1.0
```

### 3. Run the docker container
```bash
docker run -v $(pwd)/data:/firmadyne/images -it --privileged firmadyne
FROM --platform=amd64 ubuntu:22.04
```

### If you pulled the docker image, use the following command to start the container
```bash
docker run -v $(pwd)/data:/firmadyne/images -it --privileged mhrznsubin/firmadyne:1.0
```

#### Note: Place the firmware images into data directory in the host machine, it will appear in firmadyne/images directory. For testing, I have placed a sample firmware image in the data folder

### Command Usage: (Use sudo if any error occurs)

#### Once, you start the container, change the directory inside container to firmadyne
```bash
cd firmadyne
```

#### If asked for firmadyne password, use:
```bash
password: firmadyne
```

#### Extract components of firmware, use the extractor
```bash
sudo ./sources/extractor/extractor.py -b Netgear -sql 127.0.0.1 -np -nk "images/WNAP320_V3.7.11.4.zip" images
```
#### Find the ID generated during extraction in the next step. In this case it is 1 and store the result in the image table of the database.
```bash
sudo ./scripts/getArch.sh ./images/1.tar.gz
```
#### Load the contents of the filesystem for firmware 1 into the database, populating the object and object_to_image tables.
```bash
sudo ./scripts/tar2db.py -i 1 -f ./images/1.tar.gz
```
#### Create the QEMU disk image for firmware 1
```bash
sudo ./scripts/makeImage.sh 1
```
#### Infer the network configuration for firmware 1. Kernel messages are logged to ./scratch/1/qemu.initial.serial.log
```bash
sudo ./scripts/inferNetwork.sh 1
```
##### Emulate firmware 1 with the inferred network configuration. This will modify the configuration of the host system by creating a TAP device and adding a route
```bash
sudo ./scratch/1/run.sh
```
#### Run analysis
##### SNMP
```bash
sudo ./analyses/snmpwalk.sh 192.168.0.100
```
##### Web
```bash
sudo ./analyses/webAccess.py 1 192.168.0.100 log.txt
```
##### Port scan
```bash
sudo nmap -O -sV 192.168.0.100
```



