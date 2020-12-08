# Docker Container for selfhost-updater

[selfHOST](https://selfhost.de/cgi-bin/selfhost) is a german hosting service. They provide a piece of
software that updates your non-static IP address, but recently I ran into a lot of trouble with it.
So, I created my solution, called [selfhost-updater](https://gitlab.com/lazymedia/selfhost-updater).
In addition to that, I made a [Docker container](https://hub.docker.com/r/sawrz/selfhost-updater) for
selfhost-updater.

## Preparations

First, we need to prepare some things before we can start the Docker container.

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

1. Check [here](https://hub.docker.com/r/sawrz/selfhost-updater/tags) if there is a new version
available. If that's the case, change the `VERSION` variable to that version.

1. Change `USERNAME`s value to the one selfHOST gives you.
1. Change `PASSWORD`s value to the one selfHOST provides you.

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p selfhost-updater up -d
```
