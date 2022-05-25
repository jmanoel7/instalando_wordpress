# instalando_wordpress
Tutorial de como instalar wordpress no Debian GNU/Linux Bullseye

1. [Acesse o Debian GNU/Linux Buster na AWS](#acesse-o-debian-gnulinux-buster-na-aws)
2. [Atualizando o servidor](#atualizando-o-servidor)
3. [Configurando o locale](#configurando-o-locale)
4. [Instalando os pacotes Necessários](#instalando-os-pacotes-necessários)
5. [Configurando o MariaDB](#configurando-o-mariadb)
6. [Baixando o wordpress](#baixando-o-wordpress)
7. [Configurando o wordpress](#configurando-o-wordpress)
8. [Instalando o wordpress](#instalando-o-wordpress)

## Acesse o Debian GNU/Linux Buster na AWS

- Link de acesso na marketplace da AWS:

https://aws.amazon.com/marketplace/pp/prodview-vh2uh3o4pdfow?ref_=unifiedsearch

## Atualizando o servidor

```sh
sudo apt clean
sudo apt update
sudo apt upgrade
```

## Configurando o locale

```sh
sudo apt install locales
sudo sed -i 's|# pt_BR.UTF-8 UTF-8|pt_BR.UTF-8 UTF-8|' /etc/locale.gen
sudo sed -i 's|# en_US.UTF-8 UTF-8|en_US.UTF-8 UTF-8|' /etc/locale.gen
sudo locale-gen
sudo update-locale LANG="pt_BR.UTF-8" LANGUAGE="pt_BR:en_US"
```
## Instalando os pacotes necessários

- Servidor Web Apache2

```sh
sudo apt install apache2
```

- Servdor SQL MariaDB

```sh
sudo apt install mariadb-client mariadb-server
```

- Linguagem PHP

```sh
sudo apt install php7.3 php7.3-bz2 php7.3-cgi php7.3-cli php7.3-common php7.3-dev php7.3-json php7.3-mbstring php7.3-mysql php7.3-opcache \
    php7.3-phpdbg php7.3-readline php7.3-xml php7.3-xsl php7.3-zip php7.3-fpm libapache2-mod-php7
```

## Configurando o MariaDB

- Para instalação segura do MariaDB

```sh
sudo mysql_secure_installation
```

- Para logar no MariaDB como root via linha de comando

```sh
sudo mysql -u root -p
```

- Para executar a configuração do wordpress no MariaDB

```sql
CREATE DATABASE wordpress CHARACTER SET UTF8MB4 COLLATE UTF8MB4_GENERAL_CI;
CREATE USER 'wpuser'@'localhost' IDENTIFIED BY 'wpuser@123';
GRANT ALL PRIVILEGES ON wordpress.* TO 'wpuser'@'localhost';
FLUSH PRIVILEGES;
exit;
```

## Baixando o wordpress

```sh
cd ~
curl -L -O https://wordpress.org/latest.tar.gz
tar xzf latest.tar.gz
```

## Configurando o wordpress

```sh
mkdir -p ~/wordpress/wp-content/uploads
cd ~/wordpress
cp wp-config-sample.php wp-config.php
```

Em wp-config.php, onde constar as seguintes linhas:

```php
define( 'DB_NAME', 'database_name_here' );
define( 'DB_USER', 'username_here' );
define( 'DB_PASSWORD', 'password_here' );
define( 'DB_HOST', 'localhost' );
define( 'DB_CHARSET', 'utf8' );
define( 'DB_COLLATE', '' );
```

Passe a constar as seguintes linhas:

```php
define( 'DB_NAME', 'wordpress' );
define( 'DB_USER', 'wpuser' );
define( 'DB_PASSWORD', 'wpuser@123' );
define( 'DB_HOST', 'localhost' );
define( 'DB_CHARSET', 'utf8mb4' );
define( 'DB_COLLATE', 'utf8mb4_general_ci' );
```

**OBS: essas mudanças no arquivo "wp-config.php" são feitas de acordo com a configuração feita no** [MariadB](#configurando-o-mariadb)

## Instalando o wordpress

- Executar os comandos de instalação:

```sh
sudo mkdir -p /srv/www2
sudo mv ~/wordpress /srv/www2
sudo cp info.php /srv/www2
sudo chown www-data\: -R /srv/www2
sudo chmod g+w -R /srv/www2
sudo cp /etc/hosts /etc/hosts.bak
sudo cp hosts /etc/hosts
sudo cp /etc/apache2/apache2.conf /etc/apache2/apache2.conf.bak
sudo cp apache2.conf /etc/apache2/apache2.conf
sudo cp /etc/apache2/envvars /etc/apache2/envvars.bak
sudo cp envvars /etc/apache2/envvars
sudo cp /etc/apache2/ports.conf /etc/apache2/ports.conf.bak
sudo cp ports.conf /etc/apache2/ports.conf
sudo cp wordpress.conf /etc/apache2/sites-available/wordpress.conf
sudo a2ensite wordpress.conf
sudo systemctl restart apache2.service
```

- Para prosseguir com os passos finais da instalação do wordpress, favor acessar o seguinte endereço Web:

``http://localhost:8080/wordpress``

