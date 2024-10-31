# docker-container-file-system-and-volumes-persist-docker-db

-  each docker container gets its own "scratch psace" to create/update/remove
files
-  changes in one docker container won't be seen in another container
-  Volumes connect filesystem paths of a container to the host machine
-  changes in a directory mounted to a container will be reflected on the host
machine


There are two types of volumes:

1) [[docker-bind-mounts-persistent-storage]]

2) [[docker-named-volume-persistent-storage]]


