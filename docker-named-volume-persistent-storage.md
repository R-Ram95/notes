# docker-named-volume-persistent-storage

1) create volume
    ``` bash
    docker volume create <VOLUME_NAME>
    ```
2) start container but add a mount

    ``` bash
    docker run -dp 127.0.0.1:3000:3000 --mount type=volume,src=<VOLUME_NAME>,target=<PATH_TO_MOUNT> <CONTIANER_NAME>

    ```

# inspect-docker-volume

``` bash
    docker volume inspect <VOLUME_NAME>
```
