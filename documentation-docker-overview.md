---
tags:
  - Docker
---
![[/Untitled 58.png|Untitled 58.png]]

What is a container?

A standardized unit of software designed to run quickly and reliably on any computing environment running the containerization platform.

- container runtime shares the OS kernel ⇒ lets you create container images using File System layers
- containers can be spun up and spun down quicker than VMs

# Docker

- lightweight container virtualization platform
- tools to create, store, manage, and run containers
- can be integrated with automated, build, test, and deployment pipelines

benefits:

- portable runtime application environment
- packages application and dependencies into a single, immutable artifact
- run different application version with different dependencies simultaneously

What is an image?

- read-only template with instructions for creating a container
- build images on top of existing images
- each instruction in dockerfile creates a layer
    
    ![[/Untitled 1 39.png|Untitled 1 39.png]]
    

Containers are not storage units ⇒ persistent data must be stored on volumes

# Docker Best Practices

[https://snyk.io/blog/10-best-practices-to-containerize-nodejs-web-applications-with-docker/](https://snyk.io/blog/10-best-practices-to-containerize-nodejs-web-applications-with-docker/)

**NodeJS**

Use `CMD ["node", "server.js"]` instead of `CMD ["npm", "run", "start"]`

- orchestration engines need a way to send signals to the process in a container ⇒ processes run indirectly do not guarantee that the process receives these signals
- `npm run start` directly invokes the npm client which does not forward events to the node process
- multi-stage docker
    
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