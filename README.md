# sonarqube

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
   ports:
     - "9001:9000"
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
```

