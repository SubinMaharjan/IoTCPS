# Instruction to run Binwalk

### Move to Binwalk directory
```bash
cd Binwalk
```
### Build the image
```bash
docker build -t binwalk .
```
### Run the container
```bash
docker run -i -t --name="binwalk" -v $(pwd)/data:/home/ubuntu/firmware/ --rm binwalk bash
```

### Alternatively download image and run binwalk directly
```bash
docker run -it --name binwalk -v $(pwd)/data:/home/ubuntu/firmware/ mhrznsubin/binwalk:1.0 /bin/bash
```

### Usage
Place the firmware to be analyzed inside the `data` folder in the host and it will be shown in firmware folder inside the container. Finally, Run Binwalk normally. 