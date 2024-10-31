# docker-development-container-using-bind-mounts

1) mount source code into the container
2) install dependeices
3) start `nodemon` to watch for filesystem changes

Running the development container:
-  cd into directory
``` bash
    docker run -dp 127.0.0.1:3000:3000 \
        -w /app --mount type=bind,src="$(pwd)",target=/app \
        node:18-alpine \
        sh -c "yarn install && yarn run dev"
```

-  `-w` => sets the working directory to the `/app` folder
-  `-w /app --mount type=bind,src="$(pwd)",target=/app \` => bind mount current
dir of host machine into the `/app` folder of the host machine 
-  `sh -c "yarn install && yarn run dev"` => install dependencies and start dev
   server

[[docker-watch-container-logs]]

