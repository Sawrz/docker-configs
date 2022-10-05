# Docker Container for Plex

[docker-icloudpd](https://github.com/boredazfcuk/docker-icloudpd) is an An Alpine Linux Docker container for
ndbroadbent's iCloud Photos Downloader.

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here subfolders
to store your photos and videos:

``` bash
mkdir -p mnt/icloud_backup
```

### Edit the env-file

Everything you need to change before starting up the container is surrounded by `<>`.

1. Copy the `example.env` file and name it `.env`:

    ``` bash
    cp example.env .env
    ```

2. Open `.env`:

    ``` bash
    vim .env
    ```

3. Check [here](https://hub.docker.com/r/boredazfcuk/icloudpd/tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

4. Change `DATA_DIR`s value to the path you want your photos and video files to be downloaded.

5. Change `USER` and `GROUP`. They should match your hosts user and group name.

6. Set your time zone with `TIME_ZONE`

7. Change `APPLE_ID` to you Apple ID

8. Get your Telegram bot token and chat ID, and change the variables `TELEGRAM_TOKEN` and `TELEGRAM_CHAT_ID` accordingly.

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p icloud_backup up -d
```

Follow the installation instructions in the docker container:
```bash
docker container logs icloud_backup
```
