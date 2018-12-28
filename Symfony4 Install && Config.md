Install & config Syfmony 4
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

### Fix permission
```bash
$ HTTPDUSER=$(ps axo user,comm | grep -E '[a]pache|[h]ttpd|[_]www|[w]ww-data|[n]ginx' | grep -v root | head -1 | cut -d\  -f1)
$ sudo setfacl -dR -m u:"$HTTPDUSER":rwX -m u:$(whoami):rwX var
$ sudo setfacl -R -m u:"$HTTPDUSER":rwX -m u:$(whoami):rwX var
```

### Database setup
```bash
$ sudo php bin/console doctrine:database:create
```

### Yarn Install 
```bash
$ curl -sL https://deb.nodesource.com/setup_11.x | sudo -E bash -
$ sudo apt-get install nodejs -y
$ sudo npm install -g yarn
$ sudo yarn 
$ sudo yarn run encore production
```
