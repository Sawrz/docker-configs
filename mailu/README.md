# Docker Container for Mailu

[Mailu](https://github.com/mailu/mailu) is a full-featured mail server.

## Preparations

First, we need to prepare some things before we can actually start the Docker container.

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your data:

``` bash
mkdir -p certs data dav dkim filter mail overrides redis webmail
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

1. Check [here](https://hub.docker.com/r/mailu/nginx/tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

1. Change `SERVER_IP`s value to your servers' local IP. Keep in mind that it's the network IP of the PC you running
   docker on and not the IP of your docker container.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Enter the IP address of your docker containers network in `RESOLVER_IP`.

1. Change `TRAEFIK_CERTS_DIR`s value to the path you store the SSL certificates obtained by traefik.

1. Set `TRAEFIK_DOMAIN` to the URL your website is reachable, e.g., `webmail.example.com`.

1. Copy the `example.mailu.env` file and name it `mailu.env`:

    ``` bash
    cp example.mailu.env mailu.env
    ```

1. Open `mailu.env`:

    ``` bash
    vim mailu.env
    ```

1. Enter the gateway IP of your docker containers network in `SUBNET`. If your gateway is `172.1.0.1`, for example,
   enter `172.1.0.1/16`.

1. Set your domain for `DOMAIN`. For example, your webmail is reachable via `webmail.lazymedia.net`, you use for
   `DOMAIN` `lazymedia.net`.

1. Enter the URL you want your webmail to be reachable in `HOSTNAMES`, e.g., `webmail.lazymedia.net`.

1. Set your domain for `INITIAL_ADMIN_DOMAIN`, e.g., `lazymedia.net`.

1. Set `SITENAME` to a name you like.

1. Enter the URL you want your webmail to be reachable in `WEBSITE`, e.g., `webmail.lazymedia.net`.

1. Copy the `example.secrets.env` file and name it `secrets.env`:

    ``` bash
    cp example.secrets.env secrets.env
    ```

1. Open `secrets.env`:

    ``` bash
    vim secrets.env
    ```

1. Set `SECRET_KEY` to a randomly generated 16 bytes string.

1. Set `INITIAL_ADMIN_PW` to a secure password for your admin account.

1. Set `DB_PW` to your database password.

### Setup the URLs

This part has less to do with docker but more with the Domain administration. Create the following URLs on the
interface of your domain host:

1. The URL you chose for `HOSTNAMES` (from now on, we substitute this URL with `URL`)

## Run docker-compose File

Finally, start the docker container with:

``` bash
docker-compose -d -p mailu up
```

Enter your `URL`. Your username is `postmaster`, and the password is the value you gave to `INITIAL_ADMIN_PW`.

## Administration

As the administrator, you can access the admin interface via `URL/admin`.

### Add a new mail domain

1. Navigate to `Mail Domains`

1. Add a new domain by clicking on `New Domain`

1. Click on `details` in the `Actions` column. For the sake of simplicity, we call this page "Admin Page" in the
   following sections.

To keep things more trackable, we agree that we start on the Admin Page with each new subsection. Furthermore, we call
your web host domain administration page, "Domain Administration."

#### DNS MX entry

That's the first row you see on your Admin Page.

1. Create an MX entry in your Domain Administration of the newly added domain

1. Add the value you chose for `HOSTNAMES`. It should be equal to the value of the last URL in `DNS MX entry`.

#### DNS SPF entries

That's the second entry on your Admin Page. As long as you don't activate SSL, it's the last one.

1. Choose one of the `DNS SPF entries`, either `TXT` or `SPF`, and copy the string within the quotes.

1. Open Domain Administration for the newly added domain.

1. Create a new entry (`TXT` or `SPF`) in your Domain Administration, and paste the copied value to a field called
   content/target/value.

#### DKIM public key

To enable SSL for that domain, click on `Generate keys`. Three additional rows appear. This is the public key, and you
don't need to set it up in Domain Administration.

#### DNS DKIM entry

That's the fourth entry on your Admin Page. You will need the URL, which starts with `dkim._domainkey...`
(we call it `DKIM URL`) and the string in quotes (we call it `DKIM VALUE`) from `DNS DKIM entry`.

1. Open Domain Administration for the newly added domain and create a `TXT` entry.

1. Set its URL to `DKIM URL`.

1. Set its value to `DKIM VALUE`.

#### DNS DMARC entry

That's the fifth and final entry on your Admin Page. You will need the URL, which starts with `_dmarc...`
(we call it `DMARC URL`) and the string in quotes (we call it `DMARC VALUE`) from `DNS DMARC entry`.

1. Open Domain Administration for the newly added domain and create a `TXT` entry.

1. Set its URL to `DMARC URL`.

1. Set its value to `DMARC VALUE`.

#### Patience

It may take a few minutes until everything updates properly.
