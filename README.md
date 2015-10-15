# Ubuntu (Vagrant+Ansible)

Base on https://github.com/nballotta/one-command-wordpress

This is a Vagranfile plus Ansible recipe to get a starting Development Environment.

This is the list of software installed:

- Ubuntu Box with the following software
  - Nginx
  - Php5-fpm
  - Mysql
  - Various system software (likve Vim and Git)

Create two local site named

* mysite.dev
* api.mysite.dev

on 192.168.200.200

Please, find and replace your own config

## Requirements

- [VirtualBox](https://www.virtualbox.org/wiki/Downloads). Tested on 4.3.x, but 4.2.x should also work.
- [Vagrant](http://www.vagrantup.com/downloads.html). Tested on 1.6.5
- [Vagrant hostupdater](https://github.com/cogitatio/vagrant-hostsupdater).
- [Vagrant bindfs](https://github.com/gael-ian/vagrant-bindfs)
- [Ansible](http://docs.ansible.com/intro_installation.html). Tested on 1.5.5.

## Installation

1. First of all install all the required software.

2. Clone this repo.

3. Configure ansible variables in `ansible/group_vars/all` and launch Vagrant

    ```shell
    vagrant up
    ```

### Synced folders

This Vagrantfile uses bindfs to manage synced folders. This is done to avoid permission problem and to give a developer the ability to write code directly into the host folders.

## Install Laravel

First of all logged in into Ubuntu

```shell
vagrant ssh ubuntu
```
Enter as root and change dir

```shell
sudo su -
cd /var/www/mysite.dev
```

Install Composer as global

```shell
curl -sS https://getcomposer.org/installer | php
mv composer.phar /usr/local/bin/composer
```

Install Laravel as global and PATH

```shell
composer global require "laravel/installer=~1.1"
export PATH=$PATH:~/.composer/vendor/bin
```

Create a Laravel

```shell
laravel new MySite
cd MySite
mv *.* ../
rm MySite
```

## MySQL

Before edit the .env file and set database variables

```text
DB_HOST=127.0.0.1
DB_DATABASE=mysite
DB_USERNAME=root
DB_PASSWORD=password
``

then use:

```shell
php artisan migrate:install
php artisan migrate:refresh
```

See https://github.com/bestmomo/scafold for basic auth

## Additional tools

Installing nodesjs, npm and bower

```shell
apt-get update
apt-get install nodejs build-essential
ln -s /usr/bin/nodejs /usr/bin/node
apt-get install npm
npm install -g bower
```
Next, create a file `.bowerrc`

```json
{
  "directory" : "public/vendor"
}
```

In your package.json add

```json
"dependencies": {
  "react-tools": "*",
  "express": "*",
  "uglify-js": "*",
  "less": "~2.5.3"
}
```

And then

```shell
npm update
bower install
```

## Laravel views

If you would like use materialize use this syntax in blade view

```html
<!-- Materialize --->
<link href='{{ asset( 'vendor/materialize/dist/css/materialize.min.css') }}' rel='stylesheet' type='text/css'>
```
