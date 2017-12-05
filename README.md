Procédure d'installation d'un serveur web sous débian 9 (Stretch)
================================================================

### Lancement des pré-requis

1. Mise à jours des paquets
	```bash
	$ apt-get update
	$ apt-get upgrade
	```
	
2. Installation de certains outils
	```bash
	$ apt-get install git htop curl sudo -y
	```
	
3. Mise à jour de NTP (horloge système)
	```bash
	$ apt-get install ntp ntpdate -y
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
	$ apt-get install ufw -y
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
	$ apt-get install fail2ban -y
	$ sudo nano /etc/fail2ban/jail.conf 
	# activation des différents services utilisé sur le serveur fail2ban
	```

### Serveur web

1. Installation de PHP7, MariaDB et Nginx
	```bash
	$ apt-get install nginx mariadb-server php-fpm php-mysql -y
	$ systemctl enable php7.0-fpm
	```
	
2. Ajouter son vhost Nginx
	```bash
	$ cd /etc/apache2/sites-available
	$ sudo nano default
	```

3. Securité autour de Php
	```bash
	$ sudo nano /etc/php/7.0/fpm/php.ini
	```
	```bash
	# Cacher la version de PHP
	expose_php = Off
	# Augmenter la mémoire utilisée si besoin
	memory_limit = 512M
	post_max_size = 8M
	# Ajouter le phpinfo avant les autres fonctions
	disable_functions = phpinfo,...
	```
	```bash
	$ sudo nano /etc/nginx/nginx.conf
	```
	```bash
	# supprimer le commentaire # pour désactiver le server_tokens
	server_tokens off; # indique l'utilisation d'apache uniquement (sans version)
	```
	```bash
	# reload nginx
	$ systemctl reload nginx
	```
	
