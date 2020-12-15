# Docker Container for MediaWiki

[MediaWiki](https://github.com/wikimedia/mediawiki) is a free and open-source wiki engine.

## Preparations

First, we need to prepare some things before we can start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your data:

``` bash
mkdir -p data db
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

1. Check [here](https://hub.docker.com/_/mediawiki?tab=tags) if there is a new version available. If that's the
   case, change the `MEDIAWIKI_VERSION` variable to that version.

1. Check [here](https://hub.docker.com/r/thenets/parsoid) which Parsoid version you need. Update the value of `PARSOID_VERSION` if appropriate.

1. Choose a password for your databases' *root user* and set the value in `DB_ROOT_PASSWORD`.

1. Choose a password for your databases' *wikiuser user* and set the `DB_USER_PASSWORD` value. Keep in mind that the user has nothing to do with the MediaWiki user account! It's connected to the `DB_USER`, which manages all
   MediaWiki user accounts.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Choose how you want to name your Docker containers with `SERVICE_NAME`.

1. Enter the URL you want MediaWiki to be reachable in `URL`.

### Generate Config

Start the Docker container with:

``` bash
docker-compose -p mediawiki up -d
```

Keep in mind, you can exchange `mediawiki` for something else, e.g., `mediawiki-test`.

Now, open your browser and enter your PCs' IP, followed by the port `1337`. The address should look something like `192.168.0.12:1337`.

The configuration wizard page shows up. After starting the initialization, its first page, `Connect to Database`, is the most important. We substitute the values you set in `.env` in the following by the variable name:

- Database host: wiki-`SERVICE_NAME`_db
- Database name: `DB_NAME`
- Database username: `DB_USER`
- Database password: `DB_USER_PASSWORD`

Finish the wizard and choose the settings you prefer. They don't matter for the following configuration steps.

1. Download the generated `LocalSettings.php` and open it:

  ``` bash
  vim LocalSettings.php
  ```

1. Search for a line with `$wgServer` and enter the value you used for `URL`. The line should look similar to:

  ``` php
  $wgServer = "https://<URL>";
  ```

  `<URL>` and `URL` stand for the same value, but I needed to rename it due to Markdown's syntax.

1. Add the following snippet to the bottom of your `LocalSettings.php`:

  ``` php
  $wgVirtualRestConfig['modules']['parsoid'] = array(
    // URL to the Parsoid instance Use port 8142 if you use the Debian package
    'url' => 'http://parsoid:8000',
    // Parsoid "domain", see below (optional)
    'domain' => 'localhost',
    // Parsoid "prefix", see below (optional)
    'prefix' => 'localhost',
    // WARNING: ONLY enable this on private wikis and ONLY IF you understand the SECURITY IMPLICATIONS
    // of sending Cookie headers to Parsoid over HTTP. For security reasons, it is strongly recommended);
    // that $wgVirtualRestConfig['modules']['parsoid']['url'] be pointed to localhost if this setting is enabled.
    'forwardCookies' => 'true'
  );
  ```

1. Save the file.

1. Download the Visual Editor for your MediaWiki version. You find all you need [here](https://www.mediawiki.org/wiki/Special:ExtensionDistributor?extdistname=VisualEditor)

1. Shutdown and remove the Docker containers:

  ``` bash
  docker-compose -p mediawiki down
  ```

1. Extract the to `data/extensions` in your main directory, e.g.:

  ``` bash
  tar -xzf VisualEditor-<Version Number>.tar.gz -C data/extensions
  ```

1. Move your `LocalSettings.php` to `data/` in your main directory, e.g.:

  ``` bash
  mv LocalSettings.php data/
  ```

1. Remove the `VisualEditor-<Version Number>.tar.gz` since you don't need it anymore:

  ``` bash
  rm VisualEditor-<Version Number>.tar.gz
  ```

### Setup the URLs

This part has less to do with Docker but more with the Domain administration. Create the following URLs on the
interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)

### Edit the docker-compose file

Open `docker-compose.yaml`:

``` bash
vim docker-compose.yaml
```

Since you don't need local access to MediaWiki anymore, delete or set a hashtag in front of the following lines:

``` bash
    ports:
      - 1337:80
```

Save the file.

## Run docker-compose File

Finally, start the Docker containers with:

``` bash
docker-compose -p mediawiki up -d
```

Keep in mind; you can exchange `mediawiki` for something else, e.g., `mediawiki-test`.

Enter your `URL` and log in with your username and password you chose.

## Administration

This section focuses less on the administration you can do via the web-interface and more on the Docker container
administration.

You need to log into the container `wiki-<SERVICE_NAME>_mediawiki` as the user `root`:

``` bash
docker exec -u root -it wiki-<SERVICE_NAME>_mediawiki /bin/bash
```

### Updating the Database

In case you update to a newer version or install extensions that require a schema update (usually told in the docs), you need to update the database:

1. Make sure you have a backup of your database in case something goes wrong.

1. Change to the maintenance directory:

   ``` bash
   cd maintenance
   ```

1. Run `update.php`:

   ``` bash
   php update.php
   ```
