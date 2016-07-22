# Nginx/Mysql/PHP-fpm config-pack for macOS and install steps for [MacPorts](https://www.macports.org/):

## 1. Install [MacPorts](https://www.macports.org/), run selfupdate

## 2. Install git, nginx, mysql57 and php-pack:
### 2.1. PHP 5.5:
```sh
port install git +bash_completion nginx mysql57 memcached redis php55 php55-curl php55-fpm php55-xsl php55-zip php55-openssl php55-mysql php55-mbstring php55-iconv php55-intl php55-mcrypt php55-gd php55-APCu php55-memcache php55-memcached php55-oauth php55-soap php55-xdebug php55-zip
```
### 2.2. PHP 5.6:
```sh
port install git +bash_completion nginx mysql57 memcached redis php56 php56-curl php56-fpm php56-xsl php56-zip php56-openssl php56-mysql php56-mbstring php56-iconv php56-intl php56-mcrypt php56-gd php56-APCu php56-memcache php56-memcached php56-oauth php56-soap php56-xdebug php56-zip
```
### 2.3. PHP 7.0:
```sh
port install git +bash_completion nginx mysql57 memcached redis php70 php70-curl php70-fpm php70-xsl php70-zip php70-openssl php70-mysql php70-mbstring php70-iconv php70-intl php70-mcrypt php70-gd php70-APCu php70-memcache php70-memcached php70-oauth php70-soap php70-xdebug php70-zip
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

## 5. Deploy configs and load deamons (PHP 5.6 is used for example):
```sh
mkdir -p -m 777 /var/log/nginx /var/log/xdebug /var/log/php
cp mysql/my.cnf /etc/my.cnf && port load mysql
cp xdebug/xdebug-extra.ini /opt/local/var/db/php56/
chmod +x sendmail/fake_sendmail.sh && cp sendmail/fake_sendmail.sh /opt/local/bin/
cp -r php56/ /opt/local/etc/php56/ && port load php56-fpm
cp -r nginx/ /opt/local/etc/nginx/ && port load nginx
```
