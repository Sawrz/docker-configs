# Docker Container for Portainer

[Portainer](https://github.com/portainer/portainer) is a management UI for your Docker environments.

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now,
create here subfolders to store your data:

``` bash
mkdir -p data
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

1. Check [here](https://hub.docker.com/r/portainer/portainer/tags) if there is a new version
    available. If that's the case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p portainer up -d
```

Enter your PC's IP address, not the Docker containers, followed by colons, and `9000`. Assuming your
PC's IP address is `172.168.1.6`, you need to enter `172.168.1.6:9000` in your browser.
