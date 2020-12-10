# Docker Container for traefik

[traefik](https://github.com/traefik/traefik) is an HTTP reverse proxy and load balancer. Most of the
other configurations in this repository depend on it.

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your certificates:

``` bash
mkdir -p certs
```

### Edit the env-file

Everything you need to change before starting up the container is surrounded by `<>`.

1. Copy the `example.env` file and name it `.env`:

    ``` bash
    cp example.env .env
    ```

1. Open `.env`:

    ``` bash
    vim .env
    ```

1. Check [here](https://hub.docker.com/_/traefik/?tab=tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

### Edit Configuration

Open `traefik.yml` and change the `email` parameter from `<YOUR EMAIL ADDRESS>` to the Email address
you want to use for your certificates.

Finally, start the Docker container:

``` bash
docker-compose -p traefik up -d
```
