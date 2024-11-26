# sonarqube

* Increase vm.max_map_count: On your host machine, run the following command to temporarily increase the limit:

```
sudo sysctl -w vm.max_map_count=262144
```

* This change will last until the system is rebooted. To make it persistent across reboots, add the following line to `/etc/sysctl.conf`:

```
sudo nano /etc/sysctl.conf

#Update this 
vm.max_map_count=262144
```

* Then, reload the sysctl settings:

```
sudo sysctl -p
```

# Add the Compose File

```
version: "3"

services:
 sonarqube:
   image: sonarqube:lts-community
   depends_on:
     - sonar_db
   environment:
     SONAR_JDBC_URL: jdbc:postgresql://sonar_db:5432/sonar
     SONAR_JDBC_USERNAME: sonar
     SONAR_JDBC_PASSWORD: fUysjMc928ttDlf
   #ports:
   #  - "9001:9000"
   networks:
      - traefik-public
      - app-networks
   deploy:

    restart_policy:
      condition: on-failure
      delay: 5s
    labels:
      traefik.docker.network: traefik-public
      traefik.enable: "true"
      traefik.constraint-label: traefik-public
      traefik.http.routers.ai-code-review-http.rule: Host(${SITES:?No sites set})
      traefik.http.routers.ai-code-review-http.entrypoints: http
      traefik.http.routers.ai-code-review-http.middlewares: https-redirect
      traefik.http.routers.ai-code-review-https.rule: Host(${SITES})
      traefik.http.routers.ai-code-review-https.entrypoints: https
      traefik.http.routers.ai-code-review-https.tls: "true"
      traefik.http.routers.ai-code-review-https.tls.certresolver: le
      traefik.http.services.ai-code-review.loadbalancer.server.port: "9000"
   volumes:
     - sonarqube_conf:/opt/sonarqube/conf
     - sonarqube_data:/opt/sonarqube/data
     - sonarqube_extensions:/opt/sonarqube/extensions
     - sonarqube_logs:/opt/sonarqube/logs
     - sonarqube_temp:/opt/sonarqube/temp

 sonar_db:
   image: postgres:13
   environment:
     POSTGRES_USER: sonar
     POSTGRES_PASSWORD: fUysjMc928ttDlf
     POSTGRES_DB: sonar
   networks:
    - app-networks
   volumes:
     - sonar_db:/var/lib/postgresql
     - sonar_db_data:/var/lib/postgresql/data

volumes:
 sonarqube_conf:
 sonarqube_data:
 sonarqube_extensions:
 sonarqube_logs:
 sonarqube_temp:
 sonar_db:
 sonar_db_data:

networks:
  app-networks:
  traefik-public:
    external: true 
```

# Install Scanner 


* Install Java (SonarScanner requires Java):

```
sudo apt update
sudo apt install openjdk-11-jdk
```

* Download SonarScanner:

Download the latest version of SonarScanner from the official SonarSource website: https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/#download

```
wget https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.7.0.2747-linux.zip
```
* Extract the SonarScanner ZIP file:

```
unzip sonar-scanner-cli-4.7.0.2747-linux.zip
sudo mv sonar-scanner-4.7.0.2747-linux /opt/sonar-scanner
```

* Set up environment variables:

* Open `.bashrc` or `.zshrc` file:

```
nano ~/.bashrc
```
* Add the following line at the end:

```
export SONAR_SCANNER_HOME=/opt/sonar-scanner
export PATH=$SONAR_SCANNER_HOME/bin:$PATH
```

* Save the file and reload the environment:

```
source ~/.bashrc
```

* Verify the installation:

```
sonar-scanner --version
```

