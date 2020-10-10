# Docker Container for GitLab

[GitLab](https://gitlab.com/) is a web-based DevOps tool. It contains many features, like a git-repository manager,
issue tracking, and a continuous integration pipeline, to name a few.

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

1. Check [here](https://hub.docker.com/r/gitlab/gitlab-ce/tags) if there is a new version available. If that's the
   case, change the `VERSION` variable to that version.

1. Change `ROOT_DIR`s value to the path of your main directory.

1. Enter the URL you want GitLab to be reachable in `URL`.

1. Enter the SSH port you want GitLab to be reachable in `SSH_PORT`.

### Setup the URLs

This part has less to do with docker but more with the Domain administration. Create the following URLs on the
interface of your domain host:

1. The URL you chose for `URL` (from now on, we substitute the URL you chose with `URL`)
1. pages.`URL`
1. registry.`URL`

## Run docker-compose File

Finally, start the docker container with:

``` bash
docker-compose -d -p gitlab up
```

Enter your `URL` and choose a password. Log in with the username `root` and the password you chose.

## Administration

As the `root` user, you have admin rights from the beginning. However, it's good practice to change your default
username from `root` to something different. This prevents hackers from breaching your system by just guessing your
password.

### Setup GitLab Email

1. In case you use your own email server (e.g.
   [mailu](https://gitlab.com/lazymedia/docker-configs/-/blob/master/mailu/README.md)), create the following email:
   gitlab@'URL'
1. Navigate to your `config` folder (within the docker it's `/etc/gitlab/`) and edit the file `gitlab.rb`

#### System Email

Search in `gitlab.rb` for `smtp` and add your credentials. In case you use a third-party email,
[this reference](https://docs.gitlab.com/omnibus/settings/smtp.html) might be helpful with some example configurations.

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

For troubleshooting, you might want to check the [GitLab Docs](https://docs.gitlab.com/omnibus/settings/smtp.html),
[Ruby On Rails Mailer Models](https://api.rubyonrails.org/classes/ActionMailer/Base.html) and
[this Stack Overflow Question](https://stackoverflow.com/questions/21816348/553-5-7-1-mydomain-sender-address-rejected-not-owned-by-user-adminmydomain).

#### Incoming GitLab Email

Search in `gitlab.rb` for `incoming` and add your credentials. In case you use a third-party email,
[this reference](https://gitlab.com/help/administration/incoming_email.md#config-examples) might be helpful with some
example configurations.

Your configuration should look similar to this:

``` ruby
### Reply by email
###! Allow users to comment on issues and merge requests by replying to
###! notification emails.
###! Docs: https://docs.gitlab.com/ee/administration/reply_by_email.html
gitlab_rails['incoming_email_enabled'] = true

#### Incoming Email Address
####! The email address including the `%{key}` placeholder that will be replaced
####! to reference the item being replied to.
####! **The placeholder can be omitted but if present, it must appear in the
####!   "user" part of the address (before the `@`).**
gitlab_rails['incoming_email_address'] = "gitlab-incoming+%{key}@<YOUR EMAIL PROVIDER URL>"

#### Email account username
####! **With third party providers, this is usually the full email address.**
####! **With self-hosted email servers, this is usually the user part of the
####!   email address.**
gitlab_rails['incoming_email_email'] = "gitlab-incoming@<YOUR EMAIL PROVIDER URL>"

#### Email account password
gitlab_rails['incoming_email_password'] = "<YOUR EMAIL PASSWORD>"

#### IMAP Settings
gitlab_rails['incoming_email_host'] = "<YOUR EMAIL PROVIDERS EMAIL SERVER ADDRESS>"
gitlab_rails['incoming_email_port'] = 993
gitlab_rails['incoming_email_ssl'] = true
gitlab_rails['incoming_email_start_tls'] = false

#### Incoming Mailbox Settings (via `mail_room`)
####! The mailbox where incoming mail will end up. Usually "inbox".
gitlab_rails['incoming_email_mailbox_name'] = "inbox"
####! The IDLE command timeout.
gitlab_rails['incoming_email_idle_timeout'] = 60
####! The file name for internal `mail_room` JSON logfile
# gitlab_rails['incoming_email_log_file'] = "/var/log/gitlab/mailroom/mail_room_json.log"
```

### Reset the Admin Password

In case you, the first user and admin, forgot your password, log into your GitLab docker container:

``` bash
sudo docker container exec -it gitlab /bin/bash
```

Now, enter the following commands:

``` bash
su - git

gitlab-rails console

user = User.where(id: 1).first

user.password = '<new password>'

user.password_confirmation = '<new password>'

user.save

exit
```
