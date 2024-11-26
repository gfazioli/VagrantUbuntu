# Ubuntu (Vagrant+Ansible)

Base on https://github.com/nballotta/one-command-wordpress

This is a Vagranfile plus Ansible recipe to get a starting Development Environment.

This is the list of software installed:

- Ubuntu Box with the following software
  - Nginx
  - Php8.3-fpm
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
```

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
{
  "private": true,
  "devDependencies": {
    "gulp": "^3.9.0",
    "gulp-concat": "^2.6.0",
    "gulp-uglify": "^1.4.2",
    "laravel-elixir": "^3.4.1",
    "less": "~2.5.3",
    "uglify-js": "^2.5.0"
  },
  "dependencies": {
    "express": "*",
    "jquery": "^2.1.4",
    "materialize-css": "^0.97.1",
    "react": "^0.14.0",
    "react-dom": "^0.14.0",
    "react-select": "^0.7.0",
    "react-tools": "~0.13.3"
  }
}
```

And then

```shell
npm install
bower install
```

## Gulp

Example

```js
var elixir = require( 'laravel-elixir' );

/*
 |--------------------------------------------------------------------------
 | Elixir Asset Management
 |--------------------------------------------------------------------------
 |
 | Elixir provides a clean, fluent API for defining some basic Gulp tasks
 | for your Laravel application. By default, we are compiling the Sass
 | file for our application, as well as publishing vendor resources.
 |
 */

elixir( function( mix )
{
  // compile less
  mix.less( 'global.less', 'resources/assets/css/global.css' );
  mix.less( 'welcome.less', 'public/css' );
  mix.less( 'own.less', 'public/css' );

  // apps styles
  mix.styles(
    [
      './node_modules/materialize-css/dist/css/materialize.css',
      './node_modules/react-select/dist/default.css',
      'global.css',

    ],
    'public/css'
  );

  // browserify
  mix.browserify( "resources/assets/jsx/common.jsx", "resources/assets/js/common.js" );

  // components
  mix.babel( "resources/assets/jsx/materialize-preload.jsx", "resources/assets/js/materialize-preload.js" );

  // report
  mix.babel( "resources/assets/jsx/report-*.jsx", "public/js/report-tools.js" );
  mix.babel( "resources/assets/jsx/report.jsx", "public/js/report.js" );

  // move file and concats base
  mix.scripts(
    [
      'common.js',
      './node_modules/materialize-css/dist/js/materialize.js',
      './resources/assets/js/materialize-preload.js'
    ],
    'public/js'
  );
} );
```

## Laravel views

If you would like use materialize use this syntax in blade view

```html
<!-- Materialize + React Select + own --->
<link href='{{ asset( 'css/all.css') }}' rel='stylesheet' type='text/css'>

<!-- Footer -->
<script src="{{ asset( 'js/all.js' ) }}"></script>

```
