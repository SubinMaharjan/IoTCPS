# Instruction to run OpenVAS
### Once you clone the repository, change directory to OpenVAS

```bash
cd OpenVAS
```

### Run the Docker Compose file
```bash
docker compose up -d
```
### Start the vulnerability management (OpenVAS)
#### Once all the services have been started and all feed data has been loaded, start the interface.
```bash
xdg-open "http://127.0.0.1:9392" 2>/dev/null >/dev/null &
```
#### Open OpenVAS (Paste the link in the browser)
```bash
http://127.0.0.1:9392 or
http://localhost:9392
```

#### Enter the credentials
```bash
Username: admin
Password: admin
```

### Alternatively, you can use the start script to directly run the OpenVAS
```bash
curl -f -O https://greenbone.github.io/docs/latest/_static/setup-and-start-greenbone-community-edition.sh && chmod u+x setup-and-start-greenbone-community-edition.sh
./setup-and-start-greenbone-community-edition.sh

