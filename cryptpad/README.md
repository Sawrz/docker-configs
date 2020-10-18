# Docker Container for CryptPad

[CryptPad](https://github.com/xwiki-labs/cryptpad) is a zero-knowledge collaborative editor. It provides many features
you would expect from [Google Docs, Sheets, Slides, etc](https://www.google.com/sheets/about/). In contrast to Google,
however, CryptPad focuses on privacy and encrypts your data. Another benefit is that the
[Docker container](https://github.com/xwiki-labs/cryptpad-docker) runs on your server, so you have the most possible
level of control.

My main gripe with CryptPad is, you can't close the registration quickly. Therefore, I don't use it right now but keep
an eye on it.

## Preparations

First, we need to prepare some things before we can actually start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your data:

``` bash
mkdir -p data/blob data/block data/data data/files customize
```

Now, create the configuration file by copying `example.config.js`:

``` bash
cp example.config.js data/config.js
```

Finally, change the ownership of the `data` and `customize` folder to `4001`:

``` bash
sudo chown -R 4001:4001 data/ customize/
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

1. Check [here](https://hub.docker.com/r/promasu/cryptpad/tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Enter the URL you want CryptPad to be reachable in `URL`.

1. Enter a sandbox URL you want CryptPad to be reachable in `SANDBOX_URL`. That URL is not very important in production
   environments, but its included for the sake of completeness.

### Setup the URLs

This part has less to do with Docker but more with the Domain administration. Create the following URLs on the
interface of your domain host:

1. The URL you chose for `URL`

## Run docker-compose File

Finally, start the Docker container with:

``` bash
docker-compose -p cryptpad up -d
```

Open your URL and Sign Up. Now you have access to your own CrypPad instance.

## Get Admin Rights

After starting your container and signing up, you still don't have admin privileges. To get those, you need to do the following:

1. Login and navigate to `Settings`. There is a field called `Public Signing Key`. The content looks like
   `[<username>@<URL>/<key>]`

1. Open your `config.js` as a superuser:

    ``` bash
    sudo vim data/config.js
    ```

1. Navigate to `adminKeys`, add a new line and enter the data from `Public Signing Key` the following:
   `"https://<URL>/user/#/1/<username>/<key>"`

1. Restart your Docker container

After logging in, you have an admin panel on the right side.
