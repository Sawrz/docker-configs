# Docker Container for GitLab

[GitLab](https://gitlab.com/) is a web-based DevOps tool. It contains many features, like a git-repository manager, issue tracking and an continuious integration pipeline to name a few.

## Preparations

First, we need to prepare some things before we can actually start the Docker container.

### Create Volumes

Create the volumes to store the logs:

``` bash
sudo docker volume create gitlab_log
```

Check if they got created with:

``` bash
sudo docker volume ls
```

### Create Folders & Files

All files of your service will be in your main directory (the folder with the Docker files). Now, create here
subfolders to store your data:

``` bash
mkdir -p backups config data
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

1. Check [here](https://hub.docker.com/r/gitlab/gitlab-ce/tags) if there is a new version available. If true, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Enter the URL you want GitLab to be reachable in `URL`.

1. Enter the SSH port you want GitLab to be reachable in `SSH_PORT`.

### Setup the URLs

This part has less to do with docker but more with the Domain administration. Create the following URLs on the iterface of your Domain host:

1. The URL you chose for `URL` (from now on we substitute the URL you chose with `URL`)
1. pages.`URL`
1. registry.`URL`

## Run docker-compose File

Finally, start the docker container with:

``` bash
docker-compose -d -p gitlab up
```

Enter your `URL` and choose a password. Login with the username `root` and the password you chose.

## Administration

As the `root` user you have admin rights from the beginning. However, it's good practice to change your default username from `root` to something different. This prevents hackers from breaching your system by just guessing your password.

### Setup GitLab Email

1. In case you use your own email server (e.g. mailu), create the following email: gitlab@'URL'
1. Navigate to you config folder (within the docker it's `/etc/gitlab/`) and edit the file `gitlab.rb`

#### System Email

Search in that file for `smtp` and add your credentials. In case you use a third-party email, [this reference](https://docs.gitlab.com/omnibus/settings/smtp.html) might be helpful with some example configurations.

Your configuration should look similar to this:

``` ruby
### GitLab email server settings
###! Docs: https://docs.gitlab.com/omnibus/settings/smtp.html
###! **Use smtp instead of sendmail/postfix.**

gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "<YOUR PROVIDERS EMAIL SERVER ADDRESS>"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "gitlab@<YOUR EMAIL PROVIDERS URL>"
gitlab_rails['smtp_password'] = "<YOUR EMAIL PASSWORD>"

gitlab_rails['smtp_authentication'] = "plain"
gitlab_rails['smtp_enable_starttls_auto'] = true
gitlab_rails['smtp_tls'] = true
```

For troubleshooting , you might want to check the [GitLab Docs](https://docs.gitlab.com/omnibus/settings/smtp.html), [Ruby On Rails Mailer Models](https://api.rubyonrails.org/classes/ActionMailer/Base.html) and [this Stack Overflow Question](https://stackoverflow.com/questions/21816348/553-5-7-1-mydomain-sender-address-rejected-not-owned-by-user-adminmydomain).
