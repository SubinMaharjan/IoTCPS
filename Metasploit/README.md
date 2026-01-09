# Instruction to run MetaSploit

### Run the metasploit container
```bash
docker run --name metasploit -it tleemcjr/metasploitable2:latest sh -c "/bin/services.sh && bash"
```

### To connect with OpenVAS, check the network on which OpenVAS is connected to.
```bash
docker network ls
```

#### Probably, it's going to be `greenbone-community-edition_default`

#### Connect the metasploit docker to the same network
```bash
docker network connect greenbone-community-edition_default metasploit
```

### Check the IP of the container connect to the OpenVAS network
```bash
 docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' metasploit
 ```

 ### Use the IP obtained from above command for scanning in OpenVAS

 #### The image for metasploit scan is shown in <a href = "metasploit_scan.png">metasploit_scan.png</a> and the vulnerabilities are shown in <a href = "metasploit_vulnerabilities.png">metasploit_vulnerabilities.png</a> 