# docker-compose-for-multi-container-applications

-  YAML based docker configuration that creates multi-container applications
-  applications stacks are defined in a single yaml file in the root of the
project repo
-  single commands are used to spin up and tear down the application


# Creating nodejs app with SQL Server using docker-compose

``` YAML
    services:
      app:
        image: node:18-alpine
        command: sh -c "yarn install && yarn run dev"
        ports:
          - 127.0.0.1:3000:3000
        working_dir: /app
        volumes:
          - ./:/app
        environment:
          MYSQL_HOST: mysql
          MYSQL_USER: root
          MYSQL_PASSWORD: secret
          MYSQL_DB: todos

      mysql:
        image: mysql:8.0
        volumes:
          - todo-mysql-data:/var/lib/mysql
        environment:
          MYSQL_ROOT_PASSWORD: secret
          MYSQL_DATABASE: todos

    volumes:
      todo-mysql-data:
```

**app config**
-  create a port mapping for the *host machine port* to the *app* running on the *docker container port*
-  create a bind mount for the apps source files to the container *app* dir
-  add environment variables so the *app* can connect to the mysql DB

**mysql config**
-  add env variables for the DB password
-  declare the named volume for persistent data in the mysql DB


Commands:
[[docker-compose-useful-commands]]
