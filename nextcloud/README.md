# Docker Container for Nextcloud

[Nextcloud](https://nextcloud.com/) is free and open-source client-server software to create file
hosting services, like Dropbox, Google Drive, and Office 365. With various plugins, Nextcloud is
easily expandable to suit your specific needs.

## Preparations

First, we need to prepare some things before we can start the Docker container.

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

All files of your service will be in your main directory (the folder with the Docker files). Now,
create here subfolders to store your data:

``` bash
mkdir -p config db html
```

Now, create the configuration file by copying `example.nginx.conf`:

``` bash
cp example.nginx.conf config/nginx.conf
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

1. Check [here](https://hub.docker.com/_/nextcloud?tab=tags) if there is a new version available. If
   that's the case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Change `DATA_DIR`s value to the path you want Nextcloud to store the data you upload.

1. Choose a password for your databases' *root user* and set the value in `DB_ROOT_PASSWORD`.

1. Choose a password for your databases' *nextcloud user* and set the `DB_USER_PASSWORD` value. Keep
   in mind that the user has nothing to do with the Nextcloud user account! It's connected to the
   `DB_USER`, which manages all Nextcloud user accounts.

1. Enter the URL you want Nextcloud to be reachable in `URL`.

1. Choose a user name for the Nextcloud administrator and set the value in `ADMIN_USER`.

1. Choose a password for the Nextcloud administrator and set the value in `ADMIN_PASSWORD`.

### Generate Config

Start the Docker container with:

``` bash
docker-compose -p nextcloud up -d
```

### Setup the URL

This part has less to do with Docker but more with the Domain administration. Create the following
URLs on the interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)

### Configure Nextcloud

Now, you want to set up an URL and change the protocol to `https`. You must be in your main
Nextcloud directory for that.

1. Open the `config.php`:

   ``` bash
   sudo vim html/config/config.php
   ```

1. Search for a line with `overwriteprotocol` or create a new one. In the end, that line needs to
  look like this:

   ```php
   'overwriteprotocol' => 'https',
   ```

1. Search for a line with `trusted_proxies` or create a new one. In the end, that line needs to
  look like this:

   ```php
   'trusted_proxies' => 'traefik',
   ```

   Here, I assume you run my
   [traefik](https://gitlab.com/lazymedia/docker-configs/-/tree/master/traefik) container.

1. Search for an entry with `trusted_domains` and enter your `URL`s value. You want something like
  this:

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

Enter your `URL` and log in with the username you chose for `ADMIN_USER` and the password you set
`ADMIN_PASSWORD` to.

## Troubleshooting

Although running your Nextcloud instance has many benefits and frees you from handing your precious
data to other cloud providers, you may run into issues along the way. This section documents the
problems I ran into, which needed a bit of research to find a proper solution.

However, Nextclouds **Overview** tab in the settings menu is helpful as well. Read the *Security &
Setup warnings* thoroughly, and you might find the solution you need there.

### Nextcloud Server

You need to log into the container `nextcloud_server` as the user `www-data`:

``` bash
docker exec -u www-data -it nextcloud_server /bin/bash
```

#### Upgrade

If you upgrade your Nextcloud container, not everything gets updated as well, e.g., plugins. You can
fix that with a manual update:

```bash
php /var/www/html/occ upgrade
```

#### Reset Admin Password

Reset your admin password with the following command:

``` bash
php /var/www/html/occ user:resetpassword <ADMIN_USER>
```

Again, `<ADMIN_USER>` equals `ADMIN_USER`, but due to Markdown syntax, I need another way to clarify
that the value depends on the user.

Similarly, you can reset the password for any other user. However, it's easier to do that via the
web-interface.

#### Add Missing Indices to Database

If Nextcloud tells you some indices are missing in your database, run the following command:

``` bash
php /var/www/html/occ db:add-missing-indices
```

#### Update Entries in Database

In some instances, your database entries need an update. However, Nextcloud will tell you that. Run
this command to fix update them:

``` bash
php /var/www/html/occ db:convert-filecache-bigint
```

This command expands the integer bits. Read the error message/warning carefully what to convert.
Usually, it will tell you.

#### Can't delete file

Sometimes, you can't delete a file/folder in the Nextcloud interface. I recommend the following
command as a troubleshooting step:

```bash
php /var/www/html/occ files:scan --all
```

This step may take some time to finish, but you get a very detailed error message if something went
wrong.

If you get an error message, enable the maintenance mode with this command:

```bash
php /var/www/html/occ maintenance:mode --on
```

In case the error message is about a *locked file* go to the **Nextcloud Database** Subsection and
read the entry about **Unlock File**.

Remember to disable the maintenance mode after you finished your troubleshooting with:

```bash
php /var/www/html/occ maintenance:mode --off
```

#### It was not possible to execute the cron job via CLI

An error in the `Overview` tab of your administration settings might appear, telling you the data's
permissions are not right, and no .ocdata exists in that data directory.

It's always a good idea to check the permissions first. Next, check if `.ocdata` exists.

If `.ocdata` exists with correct permissions, try the following:

1. Open the `Basic Settings` tab.

1. Switch from `Cron` to `AJAX` or the other way around.

1. Run the `Overview` check again.

1. Switch back to `Cron` or `AJAX`.

### Nextcloud Database

You need to log into the container `nextcloud_db` as the user `root`:

``` bash
docker exec -u root -it nextcloud_db /bin/bash
```

#### Unlock file

Run the following command to start MariaDB:

```bash
mysql -u nextcloud -p
```

Enter the password you set for `DB_USER_PASSWORD` in your `.env` file.

Connect to the `nextcloud` database via:

```bash
connect nextcloud
```

Now, run the following command:

```sql
DELETE FROM oc_file_locks WHERE 1;
```

Finally, disable the maintenance mode as described above.
