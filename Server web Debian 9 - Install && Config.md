Procédure d'installation d'un serveur web sous débian 9 (Stretch)
================================================================

### Lancement des pré-requis

1. Mise à jours des paquets
	```bash
	$ sudo apt-get update
	$ sudo apt-get upgrade
	$ sudo apt-get dist-upgrade #MAJ system
	```
	
2. Installation de certains outils (en fonction de vos besoins)
	```bash
	$ sudo apt-get install git htop curl -y
	```
	
3. Mise à jour de NTP (horloge système) (optionnelle, voir avec la commande `$ date` si besoin)
	```bash
	$ sudo apt-get install ntp ntpdate -y
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
	$ adduser NAME_USER
	# Copie .ssh/id_rsa.pub de votre machine local 
	# Si non présent uniquement : en local $ ssh-keygen
	# Coller dans le fichier suivant
	$ nano /home/NAME_USER/.ssh/authorized_keys 
	```

2. Changement du port SSH
	```bash
	$ sudo nano /etc/ssh/sshd_config # Port 2222 par exemple
	$ sudo service ssh restart
	# on peut relancer un nouvelle session ssh avec le nouveau port
	```

3. Sécurisation des port avec un pare-feu avec UFW
	```bash
	$ sudo apt-get install ufw -y
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
	$ sudo apt-get install fail2ban -y
	$ sudo nano /etc/fail2ban/jail.conf 
	# activation des différents services utilisé sur le serveur fail2ban
	```
	
### Serveur web

1. Installation de PHP 7.0, PostgreSQL et Nginx
	```bash
	$ sudo apt-get install nginx postgresql php-fpm php-pgsql php-zip -y
	$ systemctl enable php7.0-fpm
	```
1. BIS Installation de PHP 7.3 PostgreSQL et Nginx
	```bash
	# Ajout de la list Debian Sury pour avoir php73 dans les sources apt
	$ sudo apt install ca-certificates apt-transport-https 
	$ wget -q https://packages.sury.org/php/apt.gpg -O- | sudo apt-key add -
	$ echo "deb https://packages.sury.org/php/ stretch main" | sudo tee /etc/apt/sources.list.d/php.list
	$ sudo apt-get update
	# Installation de PHP 7.3 
	$ sudo apt-get install php7.3-fpm php7.3-cli php7.3-common php7.3-curl php7.3-mbstring php7.3-pgsql php7.3-xml -y
	# Start PHP
	$ systemctl enable php7.3-fpm
	```
	
2. Ajouter son vhost Nginx
	```bash
	$ sudo nano /etc/nginx/sites-available/SITE_NAME
	$ cd /etc/nginx/sites-enabled
	$ sudo ln -s /etc/nginx/sites-available/SITE_NAME SITE_NAME
	$ sudo systemctl reload nginx
	```

3. Securité autour de Php & Nginx
	```bash
	$ sudo nano /etc/php/7.3/fpm/php.ini
	# Cacher la version de PHP
	expose_php = Off
	# Augmenter la mémoire utilisée si besoin
	memory_limit = 128M
	post_max_size = 8M
	# Ajouter le phpinfo
	disable_functions = phpinfo,...
	# reload php-fpm
	$ sudo systemctl reload php7.3-fpm
	```
	
	```bash
	$ sudo nano /etc/nginx/nginx.conf
	# supprimer le commentaire # pour désactiver le server_tokens
	server_tokens off; # indique l'utilisation d'apache uniquement (sans version)
	# reload nginx
	$ sudo systemctl reload nginx
	```
	
4. Creation role bdd
	```bash
	$ sudo -u postgres createuser -l -d -P -e DB_ROLE
	```	
