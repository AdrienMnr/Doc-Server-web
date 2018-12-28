Suchweb install & config Syfmony3
================

### Install vendors
```bash
$ php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
$ php -r "if (hash_file('sha384', 'composer-setup.php') === '93b54496392c062774670ac18b134c3b3a95e5a5e5c8f1a9f115f203b75bf9a129d5daa8ba6a13e2cc8a1da0806388a8') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
$ php composer-setup.php
$ php -r "unlink('composer-setup.php');"
$ mv composer.phar /usr/local/bin/composer
# cd dans le dossier de votre site internet /var/www/SITE_NAME
$ composer install --no-dev --optimize-autoloader
```

### Database setup
```bash
$ php bin/console doctrine:database:create
```

### Yarn Install 
```bash
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ apt-get install nodejs build-essential -y
$ npm install -g yarn
$ yarn install
$ yarn run encore production
```
