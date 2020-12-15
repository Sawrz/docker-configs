# Docker Container for Plex

[Plex](https://www.plex.tv/) is a client-server media player solution to create your personal
(non-commercial) video streaming service like Netflix, etc. Keep in mind; you need the video files on
the Plex server. So don't break any laws by obtaining them!

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now,
create here subfolders to store your configurations:

``` bash
mkdir -p config
```

Also, create two folders in the location where your video files will reside:

``` bash
mkdir -p media/original media/transcode
```

Your video files go into `media/original`.

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

1. Check [here](https://hub.docker.com/r/plexinc/pms-docker/tags) if there is a new version
    available. If that's the case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Change `DATA_DIR`s value to the path you want your video files to be.

1. Set your time zone with `TIME_ZONE`

1. Get your claim code on [plex.tv](https://www.plex.tv/) and set the variable `CLAIM_CODE`
    accordingly.

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p plex up -d
```

Enter your PC's IP address, not the Docker containers, followed by colons, and `32400`. Assuming your
PC's IP address is `172.168.1.6`, you need to enter `172.168.1.6:32400` in your browser.
