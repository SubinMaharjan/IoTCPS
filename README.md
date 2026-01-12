# Instruction to download Tools

#### Clone the repository

```bash
git clone git@github.com:SubinMaharjan/IoTCPS.git
```

#### Installation

1. For <a href="FACT/README.md"> FACT installation</a> <br>
2. For <a href="OpenVAS/README.md"> OpenVAS installation </a> <br>
3. For <a href="Metasploit/README.md"> Metasploit installation </a> <br>
4. For <a href="Binwalk/README.md"> Binwalk installation </a> <br>
5. For <a href="Firmadyne/README.md"> Firmadyne installation </a> <br>

### Enjoy !!! Happy Installation

### If you need to delete the docker containers and clear up the spaces, use the following commands:
```bash
#### Stop and delete containers
docker stop $(docker ps -aq) 2>/dev/null
docker rm $(docker ps -aq) 2>/dev/null

#### Remove all images
docker rmi $(docker images -aq) -f

#### Remove all volumes
docker volume rm $(docker volume ls -q)

#### Remove all networks
docker network rm $(docker network ls -q)

#### Delete volumes
docker system prune -a --volumes -f
```