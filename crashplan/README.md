# Docker Container for CrashPlan Pro

[CrashPlan](https://www.crashplan.com/en-us/) is a paid cloud backup solution. This
[Docker container](https://github.com/jlesage/docker-crashplan-pro) emulates the GUI of the CrashPlan application via
 VNC. So, you can access it with your web browser.

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

1. Check [here](https://hub.docker.com/r/jlesage/crashplan-pro/tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

1. If you use other Docker containers, affected folders may belong to different users and groups. Add
    those groups to `GROUP_IDS` to upload those folders to CrashPlan.

1. Adjust the maximum RAM allocation for CrashPlan with the `MAX_RAM_ALLOCATION`. I use 32 GB because
    my server has 64 GB installed.

1. Enter the path of the folder you want to backup to `BACKUP_LOCATION`.

In case you want to multiple backup folders, you need to add additional volumes of the type `bind` in the
`docker-compose.yml`. However, if local space is not a constraint, I recommend using it in combination with
[Borg](https://www.borgbackup.org/). You find a quick installation guide for Ubuntu- and Arch-based distributions
[here](https://docs.lazymedia.net/data-management/borg/).

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p crashplan up -d
```
