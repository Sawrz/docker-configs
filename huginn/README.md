# Docker Container for Huginn

[Huginn](https://github.com/huginn/huginn) a system for building agents that perform automated tasks for you online.

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your data:

``` bash
mkdir -p db
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

1. Check [here](https://hub.docker.com/r/huginn/huginn/tags) if there is a new version available. If
    that's the case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Change `DATA_DIR`s value to the path you want Nextcloud to store the data you upload.

1. Choose a password for your databases' *root user* and set the value in `DB_ROOT_PASSWORD`.

1. Choose a password for your databases' *Huginn user* and set the `DB_USER_PASSWORD` value. Keep in
    mind that the user has nothing to do with the Huginn user account! It's connected to the `DB_USER`,
    which manages all Huginn user accounts.

1. Choose a secret app token and set the value in `APP_SECRET_TOKEN`.

1. Choose an invitation code and set the value in `INVITATION_CODE`. Since it's not possible yet to
    disable user registration, you might want to keep it secret as well.

1. Enter the URL you want Huginn to be reachable in `URL`.

1. Set your time zone with `TIME_ZONE`

### Setup the URL

This part has less to do with Docker but more with the Domain administration. Create the following
URLs on the interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)

### Generate Config

Start the Docker container with:

``` bash
docker-compose -p huginn up -d
```
