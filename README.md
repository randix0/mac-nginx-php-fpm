# Nginx/Mysql/PHP-fpm config-pack for macOS and install steps for [MacPorts](https://www.macports.org/):

## 0. Install [MacPorts](https://www.macports.org/), run selfupdate; open Terminal.app preferences, select Command and set:
```sh
/opt/local/bin/bash -l
```

## 1. Install git, nginx, mysql57 and php-pack:
### 1.1. PHP 5.5:
```sh
sudo port install git +bash_completion nginx mysql57-server memcached redis php55 php55-curl php55-fpm php55-xsl php55-zip php55-openssl php55-mysql php55-mbstring php55-iconv php55-intl php55-mcrypt php55-gd php55-APCu php55-memcache php55-memcached php55-oauth php55-soap php55-xdebug php55-zip
```
### 1.2. PHP 5.6:
```sh
sudo port install git +bash_completion nginx mysql57-server memcached redis php56 php56-curl php56-fpm php56-xsl php56-zip php56-openssl php56-mysql php56-mbstring php56-iconv php56-intl php56-mcrypt php56-gd php56-APCu php56-memcache php56-memcached php56-oauth php56-soap php56-xdebug php56-zip
```
### 1.3. PHP 7.0:
```sh
sudo port install git +bash_completion nginx mysql57-server memcached redis php70 php70-curl php70-fpm php70-xsl php70-zip php70-openssl php70-mysql php70-mbstring php70-iconv php70-intl php70-mcrypt php70-gd php70-APCu php70-memcache php70-memcached php70-oauth php70-soap php70-xdebug php70-zip
```
## 2. Setup MySQL 5.7 server:
```sh
sudo /opt/local/lib/mysql57/bin/mysqld --initialize --user=_mysql
sudo port load mysql57-server
sudo /opt/local/lib/mysql57/bin/mysql_secure_installation
```

## 3. Clone these configs using the next commands:
```sh
mkdir mac-nginx-php-fpm
cd mac-nginx-php-fpm/
git clone https://github.com/randix0/mac-nginx-php-fpm.git .
```

## 4. Customize configs before deploying:
### 4.1. Set choosen PHP version (php-fpm.5.5.sock, php-fpm.5.6.sock, php-fpm.7.0.sock) in upstream backend node at nginx/nginx.conf
```sh
	upstream backend {
		server unix:/var/run/php-fpm.5.6.sock;
	}
```
### 4.2. Set your path in the nexts files:
- nginx/sites-enabled/auto.conf
- nginx/sites-enabled/auto_ssl.conf.dist (rename to .conf to enable)
- nginx/sites-enabled/root.conf.dist (rename to .conf to enable)

## 5. Deploy configs and load daemons (PHP 5.6 is used for example):
```sh
sudo mkdir -p -m 777 /var/log/nginx /var/log/xdebug /var/log/php
sudo cp mysql57/my.cnf /opt/local/etc/mysql57/my.cnf && sudo port unload mysql57-server && sudo port load mysql57-server
sudo cp xdebug/xdebug-extra.ini /opt/local/var/db/php56/
sudo chmod +x sendmail/fake_sendmail.sh && cp sendmail/fake_sendmail.sh /opt/local/bin/
mkdir -p ~/Sites/php.lo/ && touch ~/Sites/php.lo/index.php
sudo cp -r php56/ /opt/local/etc/php56/ && sudo port load php56-fpm
sudo cp -r nginx/ /opt/local/etc/nginx/ && sudo port load nginx
printf "if [ -f /opt/local/share/bash-completion/bash_completion ]; then \n . /opt/local/share/bash-completion/bash_completion \n fi" >> ~/.profile
sudo ln -s /opt/local/bin/php56 /usr/local/bin/php
php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
php -r "if (hash_file('SHA384', 'composer-setup.php') === 'e115a8dc7871f15d853148a7fbac7da27d6c0030b848d9b3dc09e2a0388afed865e6a3d6b3c0fad45c48e2b5fc1196ae') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
php composer-setup.php
php -r "unlink('composer-setup.php');"
sudo chmod +x ./composer.phar && mv ./composer.phar /opt/local/bin/composer
echo "<?php phpinfo();" >> ~/Sites/php.lo/index.php
echo "127.0.0.1    php.lo" | sudo tee -a /etc/hosts
curl -I http://php.lo/
```
