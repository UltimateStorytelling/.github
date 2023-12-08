docker-compose.ymL<br>

```

version: "3"
services:
  web:
    container_name: nginx
    image: nginx
    ports:
      - "80:80"
    volumes:
      - ./backend/nginx/conf.d:/etc/nginx/conf.d
    depends_on:
      - backend
      - frontend
    networks:
      - local-network

  database:
    container_name: database
    image: mysql:8.1.0
    restart: unless-stopped
    environment:
        MYSQL_DATABASE: ultimatedb
        MYSQL_USER: mayaadmin
        MYSQL_PASSWORD: mayaadmin
        MYSQL_ROOT_HOST: '%'
        MYSQL_ROOT_PASSWORD: 1234
    ports:
        - "3306:3306"
    volumes:
      - ./backend/mysql/conf.d:/etc/mysql/conf.d
      - mysql-data:/var/lib/mysql
    command:
      - "--character-set-server=utf8mb4"
      - "--collation-server=utf8mb4_unicode_ci"
    networks:
      - local-network

  backend:
    container_name: backend-application
    restart: on-failure
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://database:3306/ultimatedb?createDatabaseIfNotExist=true&useUnicode=true&characterEncoding=UTF-8&characterSetResults=UTF-8&useSSL=true
      SPRING_DATASOURCE_USERNAME: "mayaadmin"
      SPRING_DATASOURCE_PASSWORD: "mayaadmin"
    depends_on:
      - database
    networks:
      - local-network
  frontend:
    container_name: frontend-application
    restart: on-failure
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    depends_on:
      - backend
    networks:
      - local-network
networks:
  local-network:

volumes:
    mysql-data:

```

이후 실행방법

1. 최상위 풀더에서 docker compose up --build
2. 이후 컨테이너에서 빌드된 이미지를 실행/중지
