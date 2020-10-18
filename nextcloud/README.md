# Docker Container for Nextcloud

[Nextcloud](https://nextcloud.com/) is free and open-source client-server software to create file hosting services,
like Dropbox, Google Drive, and Office 365. With various plugins, Nextcloud is easily expandable to suit your specific
needs.

## Preparations

First, we need to prepare some things before we can actually start the Docker container.

### Create Volumes

Create the volumes to store the data of Redis:

``` bash
sudo docker volume create nextcloud-redis_data
```

Check if they got created with:

``` bash
sudo docker volume ls
```

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your data:

``` bash
mkdir -p config db html
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

1. Check [here](https://hub.docker.com/_/nextcloud?tab=tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Change `DATA_DIR`s value to the path you want Nextcloud to store the data you upload.

1. Choose a password for your databases' *root user* and set the value in `DB_ROOT_PASSWORD`.

1. Choose a password for your databases' *nextcloud user* and set the `DB_USER_PASSWORD` value. Keep in mind that the
   user has nothing to do with the Nextcloud user account! It's connected to the `DB_USER`, which manages all
   Nextcloud user accounts.

1. Enter the URL you want GitLab to be reachable in `URL`.

1. Choose a user name for the Nextcloud administrator and set the value in `ADMIN_USER`.

1. Choose a password for the Nextcloud administrator and set the value in `ADMIN_PASSWORD`.

### Start your Docker container to generate configuration files

Start the docker container with:

``` bash
docker-compose -d -p nextcloud up
```

### Setup the URL

This part has less to do with docker but more with the Domain administration. Create the following URLs on the
interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)

### Configure Nextcloud

Now, you want to set up an URL and change the protocol to `https`. You need to be in your main Nextcloud directory for
that.

1. Open the `config.php`:

   ``` bash
   sudo vim html/config/config.php
   ```

1. Search for a line with `overwriteprotocol` or create a new one. In the end that line needs to look like this:

   ```php
   'overwriteprotocol' => 'https',
   ```

1. Sarch for an entry with `trusted_domains` and enter your `URL`s value. You want something like this:

    ```php
    'trusted_domains' => array (
        0 => '<URL>',
    ),

    ```

    `<URL>` and `URL` stand for the same value, but I needed to rename it due to Markdown's syntax.

1. Finally, restart your Docker containers:

   ``` bash
   docker-compose restart nextcloud
   ```

   `nextcloud_server` should restart now.

## Login to Nextcloud

Enter your `URL` and log in with the username you chose for `ADMIN_USER` and the password you set `ADMIN_PASSWORD` to.

## Administration

This section focuses less on the administration you can do via the web-interface and more on the Docker container
administration.

You need to log into the container `nextcloud_server` as the user `www-data`:

``` bash
docker exec -u www-data -it nextcloud_server /bin/bash
```

### Reset Admin Password

Reset your admin password with the following command:

``` bash
php /var/www/html/occ user:resetpassword <ADMIN_USER>
```

Again, `<ADMIN_USER>` equals `ADMIN_USER`, but due to Markdown syntax, I need another way to clarify that the value
depends on the user.

Similarly, you can reset the password for any other user. However, it's easier to do that via the web-interface.

### Troubleshooting

Sometimes you get error messages, or the Nextcloud won't start properly. Again, most of the solutions need you to log
into `nextcloud_server`. In rare cases, you don't. I will mention that explicitly.

#### Add Missing Indices to Database

If Nextcloud tells you some indices are missing in your database, run the following command:

``` bash
php /var/www/html/occ db:add-missing-indices
```

#### Update Entries in Database

In some instances, your database entries need an update. However, Nextcloud will tell you that. Run this command to
fix update them:

``` bash
php /var/www/html/occ db:convert-filecache-bigint
```

This command expands the integer bits. Read the error message/warning carefully what to convert. Usually, it will tell
you.

#### It was not possible to execute the cron job via CLI

An error in the `Overview` tab of your administration settings might appear, telling you the data's permissions are
not right, and no .ocdata exists in that data directory.

It's always a good idea to check the permissions first. Next, check if `.ocdata` exists. If both are correctly set,
try the following:

1. Open the `Basic Settings` tab.

1. Switch from `Cron` to `AJAX` or the other way around.

1. Run the `Overview` check again.

1. Switch back to `Cron` or `AJAX`.
