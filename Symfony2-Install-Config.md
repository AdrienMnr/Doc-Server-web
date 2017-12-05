Suchweb install & config Syfmony3
================

### Install vendors
```bash
$ apt-get install php-curl -y
$ curl -sS https://getcomposer.org/installer | php
$ mv composer.phar /usr/local/bin/composer
$ composer install
```
	
### Directories
```bash
$ apt-get install acl -y
$ HTTPDUSER=$(ps axo user,comm | grep -E '[a]pache|[h]ttpd|[_]www|[w]ww-data|[n]ginx' | grep -v root | head -1 | cut -d\  -f1)
# if this doesn't work, try adding `-n` option
$ sudo setfacl -dR -m u:"$HTTPDUSER":rwX -m u:$(whoami):rwX var
$ sudo setfacl -R -m u:"$HTTPDUSER":rwX -m u:$(whoami):rwX var
```

### Database setup
```bash
$ php bin/console doctrine:database:create
$ chmod u+x reset_db.sh
$ .bin/reset_db.sh
$ chmod u-x reset_db.sh
```

### Checking your configuration & optimize setup
```bash
$ SYMFONY_ENV=prod composer install --no-dev --optimize-autoloader
```

### Yarn Install 
```bash
$ curl -sL https://deb.nodesource.com/setup_8.x | sudo -E bash -
$ apt-get install nodejs build-essential -y
$ npm install -g yarn
$ yarn install
$ yarn run encore production
```
