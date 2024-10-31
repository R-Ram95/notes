# starting-nodejs-with-docker-best-practice


**NodeJS**

Use `CMD ["node", "server.js"]` instead of `CMD ["npm", "run", "start"]`

-  orchestration engines need a way to send signals to the process in a container ⇒ processes run indirectly do not guarantee that the process receives these signals
-  `npm run start` directly invokes the npm client which does not forward events to the node process
-  multi-stage docker
    
    - [https://docs.docker.com/build/building/multi-stage/](https://docs.docker.com/build/building/multi-stage/)
    
    ```JavaScript
    # syntax=docker/dockerfile:1
    FROM golang:1.21
    WORKDIR /src
    COPY <<EOF ./main.go
    package main
    
    import "fmt"
    
    func main() {
      fmt.Println("hello, world")
    }
    EOF
    RUN go build -o /bin/hello ./main.go
    
    FROM scratch
    COPY --from=0 /bin/hello /bin/hello
    CMD ["/bin/hello"]
    ```
    
    > The `COPY --from=0` line copies just the built artifact from the previous stage into this new stage.
