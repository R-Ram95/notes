# pushing-docker-image-to-docker-hub-and-tagging-a-docker-image

-  go to docker hub
-  create a repository
-  set visibility to public ?
-  select Create
-  add a tag to the docker image
    ``` bash
        docker tag <TAG_NAME> <DOCKER_USER_NAME>/<IMAGE_NAME>
    ```
-  push the image
    ``` bash
        docker push <DOCKER_USER_NAME>/<IMAGE_NAME>
    ```
