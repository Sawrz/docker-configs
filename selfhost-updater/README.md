# Docker Container for selfhost-updater

[selfHOST](https://selfhost.de/cgi-bin/selfhost) is a german hosting service. They provide a piece of software that updates your non-static IP address if you rent a domain there. I wanted to avoid the manual installation every time I switch machines, so I created this [Docker container](https://gitlab.com/lazymedia/selfhost-updater).

## Preparations

First, we need to prepare some things before we can actually start the Docker container.

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

1. Check [here](https://hub.docker.com/r/sawrz/selfhost-updater/tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

1. Change `USERNAME`s value to the one selfHOST gives you.
1. Change `PASSWORD`s value to the one selfHOST provides you.

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p selfhost-updater up -d
```
