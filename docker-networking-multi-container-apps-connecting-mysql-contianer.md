# docker-networking-multi-container-apps-connecting-mysql-container

-  containers can talk to each other using docker networking

How to start a mysql server using Docker

1) Create a network

    ``` bash
        docker network create <network-name>
    ```

2) Start MySQL server and attach it to the network

    ``` bash
        docker run -d \
        --network todo-app --network-alias mysql \
        -v todo-mysql-data:/var/lib/mysql \
        -e MYSQL_ROOT_PASSWORD=secret \
        -e MYSQL_DATABASE=todos \
        mysql:8.0
    ```

3) Confirm DB is up and running

    ``` bash
        docker exec -it <mysql-container-id> mysql -u root -p
    ```
    -  starts interactive mysql shell in the container

    ```mysql
        SHOW DATABASES;
    ```


Connect to MySQL
-  how do we find the container we just created on the network?
    -  using the https://github.com/nicolaka/netshoot container
    -  this container ships with tools to troubleshoot and debug network issues

1) Start new container w/ an interactive shell using the nocolaka/netshoot image
    
    ``` bash
        docker run -it --network todo-app nicolaka/netshoot
    ```
2) use the `dig` DNS command which displays A name records for the given
hostname
    
    ``` bash
        dig mysql
    ```
    
    -  in the output we see an A record that maps the `mysql` host name to
    `172.23.0.2` => when we go to connect to this container, we can just specify
    the hostname (`mysql`)

    OUTPUT
        ```
            ; <<>> DiG 9.18.8 <<>> mysql
            ;; global options: +cmd
            ;; Got answer:
            ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32162
            ;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 0

            ;; QUESTION SECTION:
            ;mysql.				IN	A

            ;; ANSWER SECTION:
            mysql.			600	IN	A	172.23.0.2

            ;; Query time: 0 msec
            ;; SERVER: 127.0.0.11#53(127.0.0.11)
            ;; WHEN: Tue Oct 01 23:47:24 UTC 2019
            ;; MSG SIZE  rcvd: 44
    ```

Run app with MySQL

-  Start the todo app and connect it to the app network

    ``` bash
        docker run -dp 127.0.0.1:3000:3000 \
          -w /app -v "$(pwd):/app" \
          --network todo-app \
          -e MYSQL_HOST=mysql \
          -e MYSQL_USER=root \
          -e MYSQL_PASSWORD=secret \
          -e MYSQL_DB=todos \
          node:18-alpine \
          sh -c "yarn install && yarn run dev"
    ```
    
    -  when we look at the logs, we will see that the app is connected to the
    mysql server we made


