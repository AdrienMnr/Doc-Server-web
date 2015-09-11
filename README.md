Procédure d'installation d'un serveur web sous débian 8 (jessie)
================================================================

### Lancement des pré-requis

1. Mise à jours des paquets
	```bash
	$ aptitude update
	$ aptitude upgrade
	```
	
2. Installation de certains outils
	```bash
	$ aptitude install git vim htop curl sudo -y
	```
	
3. Mise à jour de NTP (horloge système)
	```bash
	$ aptitude install ntp ntpdate -y
	$ sudo service ntp stop
	$ ntpdate 0.fr.pool.ntp.org # mise à jour de la date et heure FR
	$ sudo nano /etc/ntp.conf
	```
	```bash
	# copier la liste des serveurs français au dessus des serveurs présents
	server 0.fr.pool.ntp.org
	server 1.fr.pool.ntp.org
	server 2.fr.pool.ntp.org
	server 3.fr.pool.ntp.org
	```
	```bash
	$ sudo service ntp start
	```

### Sécurité

1. Changement du mot de passe root et utilisateur lors de l'installation du serveur :
	```bash
	$ passwd root
	$ passwd USER # Pour chaque user présent sur le serveur
	```

2. Changement du port SSH
	```bash
	$ sudo nano /etc/ssh/sshd_config # Port 2222 par exemple
	$ sudo service ssh restart
	# on peut relancer un nouvelle session ssh avec le nouveau port
	```

3. Sécurisation des port avec un pare-feu avec UFW
	```bash
	$ aptitude install ufw
	```
	```bash
	# Commande de base UFW
	$ ufw enable # activation
	$ ufw disable # désactivation
	$ ufw status # status du service
	$ ufw status verbose # status + règles IN / OUT
	# Règles de base à installer
	$ ufw allow 80 # port web
	$ ufw allow 2222 # port ssh (en fonction du paramètrage ssh)
	$ ufw allow 443 # port https
	$ ufw allow 25 # port mail
	$ ufw default deny # désactivation de tout les autres port par défaut
	$ ufw enable
	```

4. Fail2Ban
	```bash
	$ aptitude install fail2ban -y
	$ sudo nano /etc/fail2ban/jail.conf 
	# activation des différents services utilisé sur le serveur
	```

### Serveur web

1. Installation d'Apache 2
	```bash
	$ aptitude install apache2 apache2-doc apache2-mpm-prefork apache2-utils -y
	$ a2enmod rewrite expires headers deflate 
	```
	
2. Supprimer le vhost actuel
	```bash
	$ cd /etc/apache2/sites-available
	$ a2dissite 000-default.conf
	```

3. Installation de PHP 5 et de ses librairies 
	```bash
	$ aptitude install libapache2-mod-php5 php5 php5-common php5-curl php5-dev php5-gd php5-imagick php5-imap php5-json php5-mcrypt php5-memcache php5-mhash php5-ming php5-mysql php5-pspell php5-sqlite php5-xsl -y
	```

4. Securité autour de Php
	```bash
	$ sudo nano /etc/php5/apache2/php.ini
	```
	```bash
	# Cacher la version de PHP
	expose_php = Off
	# Augmenter la mémoire utilisée
	memory_limit = 512M
	# Utile pour l'import de grosses bases de données
	post_max_size = 128M
	# Ajouter le phpinfo avant les autres fonctions
	disable_functions = phpinfo,...
	```
	```bash
	sudo nano /etc/apache2/conf-enabled/security.conf
	```
	```bash
	ServerTokens Prod # indique l'utilisation d'apache uniquement (sans version)
	ServerSignature Off # masque la version apache en cas d'erreur serveur
	TraceEnable Off # Désactive les requêtes de type TRACE
	```
	
5. On redémare enfin le serveur apache
	```bash
	sudo service apache2 restart
	```

6. Installation de MySQL
	```bash
	aptitude install mysql-server -y
	# suivre les instructions 
	```

7. Installation de phpMyAdmin
	```bash
	$ aptitude install phpmyadmin -y
	$ sudo ln -s /etc/phpmyadmin/apache.conf /etc/apache2/sites-enabled/phpmyadmin.conf
	$ sudo service apache2 restart
	```
