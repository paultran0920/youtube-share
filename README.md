## Run on local

`docker-compose -f docker-compose-local.yml up`

## Run on dev => It should similar to local

`docker-compose -f docker-compose-dev.yml up`

## Run on PRO

**Highly recommended**: let setup to run this project dirrectly on the EC2 to have better performance

### Product deployment strategy

1. BE will be downloaded and run dirrectly using Node server
2. FE will be downloaded, build and run dirrectly using Node server
3. Using nginx as proxy server, the default should be

- https://domain => will redirect to the FE/admin web site
- https://domain/api => will redirect to the BE api server

## Apendix

`docker-compose-local.yml` file

```yml
version: '3.7'

networks:
  DoorSuisse:
    ipam:
      config:
        - subnet: 172.8.0.0/24

services:
  mysql:
    container_name: door-suisse-mysql
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_DATABASE: door_suisse_db
      MYSQL_USER: door_suisse_user
      MYSQL_PASSWORD: door_suisse_passowrd
      MYSQL_ROOT_PASSWORD: door_suisse_passowrd
    volumes:
      - ./db_data:/var/lib/mysql
    networks:
      - DoorSuisse
    logging:
      options:
        max-size: 100m
    ports:
      - 13306:3306

  admin:
    container_name: door-suisse-admin
    build:
      context: ./../door-suisse-admin
      args:
        - APP_BE_ROOT=https://door-suisse-be:8080
    logging:
      options:
        max-size: 100m
    ports:
      - 18081:8080
    networks:
      - DoorSuisse

  backend:
    container_name: door-suisse-be
    build:
      context: ./../door-suisse-be
    volumes:
      - ./configurations/be/local:/usr/app/local
      - ./logs/door-suisse-be:/usr/app/local/logs
    logging:
      options:
        max-size: 100m
    ports:
      - 18082:8080
    networks:
      - DoorSuisse
```
