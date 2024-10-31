# docker-compose-useful-commands

Start application stack (in detached mode)

``` bash
    docker compose up -d
```

View logs

```bash
    docker compose logs -f
```
-  displays logs for all containers running in the stack in a single stream

View logs for single container

```bash
    docker compose logs -f <service_name>
```

Tear down

```bash
    docker compose down
```
-  volumes are not removed by default

Tear down including volumes

```bash
    docker compose down --volume
```

