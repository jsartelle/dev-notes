---
{"dg-publish":true,"dg-path":"Cheat sheets/Docker.md","permalink":"/cheat-sheets/docker/"}
---


# Run docker-compose file

- sets up multiple images
- can leave out the `-f` argument if the file is named `docker-compose.yml`

```shell
docker-compose -f docker-compose.yml up
```

# Build image from Dockerfile

- can leave out the `-f` argument if the file is named `Dockerfile`

```shell
docker build -t image_name -f Dockerfile.dev .
```

# Create container from image

- `-p` maps port 4001 in the container to port 3000 in the host
- `-e` sets environment variables
    - if `=` is not present it uses the value from the parent shell

```shell
docker run -p 127.0.0.1:3000:4001 -e VAR1 -e VAR2=foo image_name
```
