# Docker Container for Taiga

[Taiga](https://www.taiga.io/) is a free and open-source project management tool.

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your data:

``` bash
mkdir -p db media
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

1. Check [here](https://quay.io/repository/riotkit/taiga?tab=tags) if there is a new version available. If that's
the case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Choose a password for your databases' *taiga user* and set the `DB_USER_PASSWORD` value. Keep in mind that the user has nothing to do with the Taiga user account! It's connected to the `DB_USER`, which manages all Taiga user accounts.

1. Choose a secret key and set the value in `SECRET_KEY`.

1. Enter the Email address you want to use for emails from Taiga in `TAIGA_EMAIL_FROM`.

1. Enter the Email server address for outgoing emails in `TAIGA_EMAIL_HOST`.

1. Enter the Email user name (the one you use for register in the web-interface) in `TAIGA_EMAIL_USER`.

1. Enter the Email user password (the one you use for register in the web-interface) in `TAIGA_EMAIL_PASS`.

1. Enter the URL you want Nextcloud to be reachable in `URL`.

### Setup the URL

This part has less to do with Docker but more with the Domain administration. Create the following URLs on the
interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p taiga up -d
```

Enter your `URL` and sign up with a user name and password of your choice.

## Administration

To access the administration interface, you need to enter `URL/admin/`. Enter your users' credentials to log in.
