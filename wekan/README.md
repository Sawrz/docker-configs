# Docker Container for Nextcloud

[Wekan](https://www.github.com/wekan/wekan) is a free and open-source collaborative kanban board
application. Its feature set and its interface are similar to that of [Trello](https://trello.com/).

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now,
create here subfolders to store your data:

``` bash
mkdir -p db/data db/dump
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

1. Check [here](https://hub.docker.com/r/wekanteam/wekan/tags) if there is a new version available.
    If that's the case, change the `WEKAN_VERSION` variable to that version.

1. Check [here](https://hub.docker.com/_/mongo?tab=tags) if there is a new version available. If
    that's the case, change the `MONGODB_VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Enter the URL you want Wekan to be reachable in `URL`.

### Setup the URL

This part has less to do with Docker but more with the Domain administration. Create the following
URLs on the interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p wekan up -d
```

Enter your `URL` and create a user who becomes the administrator.
