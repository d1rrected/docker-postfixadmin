# Postfix.Admin docker image

This repository contains a docker image for
[Postfixadmin](http://postfixadmin.sourceforge.net/) that you can download
from [Docker Hub](https://hub.docker.com/r/zedtux/postfixadmin/).

# Build the image

As the image is not finished, I didn't pushed it to docker.com so you need to
build it yourself.

    $ git clone https://github.com/zedtux/docker-postfixadmin
    $ cd docker-postfixadmin/
    $ sudo docker build -t <username>/postfixadmin .

# Image usage

First start a MySQL image

    sudo docker run --name mysql -e MYSQL_ROOT_PASSWORD=test -d mysql

Run a first time the image in order to generate the setup password hash
(this will gives you the value for the environment variable `SETUP_PASSWORD`):

    sudo docker run --rm \
      --link mysql:mysql \
      -e MYSQL_ROOT_PASSWORD=test \
      -e MYSQL_DB_PASSWORD=zedtux \
      -h 'mail.zedroot.org' \
      zedtux/postfixadmin

Now visit the URL `http://<docker-host>/setup.php`. It should create the MySQL
database and ask you for the setup password.
Enter a password and click the "Generate password hash".
This will update the page and gives you the setup password to use in the next
step. It's a very long string composed of alphanumeric characters.

Then start the Postfixadmin image with the `SETUP_PASSWORD`

    sudo docker run --name postfixadmin \
      --link mysql:mysql \
      -e MYSQL_ROOT_PASSWORD=test \
      -e MYSQL_DB_PASSWORD=zedtux \
      -e SETUP_PASSWORD=<setuppassword> \
      -h 'mail.zedroot.org' \
      zedtux/postfixadmin

Now visiting the URL `http://<docker-host>/setup.php` should show you the admin
account creation form.
Type your setup password, the email address of your admin user and its password.
If all is okay, you will see a message "The admin <email> has been added!"

Now visit the URL `http://<docker-host>/` and login using the created account.
