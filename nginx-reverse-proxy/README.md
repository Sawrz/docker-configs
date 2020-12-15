# Docker Container for NGINX reverse proxy

[NGINX](https://nginx.org/en/) is an HTTP and reverse proxy server. I got rid of the NGINX reverse
proxy in favor of [traefik](https://gitlab.com/lazymedia/docker-configs/-/tree/master/traefik). If
you prefer this reverse proxy, you need to edit all configuration files in this repository that use
an URL. To do that, you need to remove the `traefik` labels in those `docker-compose.yaml` files and
add the environment variable `VIRTUAL_HOST` to them. You find more information
[here](https://hub.docker.com/r/jwilder/nginx-proxy).

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now,
create here subfolders to store your certificates:

``` bash
mkdir -p config certs
```

Now, create a configuration file that expands your uploadsize by copying `example.uploadsize.conf`:

``` bash
cp example.uploadsize.conf config/uploadsize.conf
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

1. Check [here](https://hub.docker.com/r/jwilder/nginx-proxy/tags?page=1&ordering=last_updated) if
    there is a new version available. If that's the case, change the `VERSION` variable to that
    version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Change `EMAIL`s value from `<YOUR EMAIL ADDRESS>` to the Email address you want to use for your
    certificates.

## Run docker-compose File

Finally, start the Docker container:

``` bash
docker-compose -p nginx-reverse-proxy up -d
```
