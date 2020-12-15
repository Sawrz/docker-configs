# Docker Container for WebDAV

[WebDAV](http://www.webdav.org/) lets you access your files via your web browser.

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now,
create here as many subfolders as you like to store your data. For the sake of simplicity, I stick
with one folder:

``` bash
mkdir -p data/files
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

1. Check [here](https://hub.docker.com/r/bytemark/webdav/tags) if there is a new version available.
  If that's the case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Choose a username for your WebDAV service and set the value in `USERNAME`.

1. Choose a password for your WebDAV service and set the value in `PASSWORD`.

1. Enter the URL you want WebDAV to be reachable in `URL`.

### Edit docker-compose-file

If you added more than one directory in data or named it differently from `files`, open
`docker-compose.yaml`.

#### Different folder name

Go to the `volumes` section and search for `type: bind`. Underneath, there are two more entries
called `target` and `source`. You want to change the entry in `source` after the last `/`.

For example, you named your directory `cat_pictures`. Then you changed the entry in `source` from
`${ROOT_DIR}/data/files` to `${ROOT_DIR}/data/cat_pictures`.

#### Multiple folders

In case you added multiple folders, copy the whole `bind` block and paste it below, e.g.,

``` yaml
- type: bind
  target: /var/lib/dav/data/files
  source: ${ROOT_DIR}/data/files
- type: bind
  target: /var/lib/dav/data/files
  source: ${ROOT_DIR}/data/files
```

Now, follow the instruction above on how to use a different folder name.

### Setup the URL

This part has less to do with Docker but more with the Domain administration. Create the following
URLs on the interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)

## Run docker-compose File

Start the Docker container with:

``` bash
docker-compose -p webdav up -d
```
