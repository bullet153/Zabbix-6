# Zabbix-6
Instalando o zabbix 6.0 no Debian 11 


##Aqui temos um exemplo de como instalar o Zabbix 6 no Debian 11 - usando o mariadb

Script para instalação do zabbix no Debian

usuário - 
senha: - 

instalação padrão com LVM ativo
Instalar com a partição completa /home/var e /etc no mesmo local

adduser
passwd

apt update -y
apt upgrade -y
apt install net-tools
apt install sudo -y
visudo /etc/sudores
apt install vim 

sudo visudo

user ALL=(ALL:ALL) ALL - precisa de senha de root (safe)*
user ALL=(ALL:ALL) NOPASSWD:ALL - nao precisa de senha de root

instalar pacote do openssh caso necessário 

--------------------------------------------
Instalando o apache

apt install apache2 php php-mysql php-mysqlnd php-ldap php-bcmath php-mbstring php-gd php-pdo php-xml libapache2-mod-php -y

systemctl status apache2

#Verifica se o apache2 esta funcionando

Instalação do servidor e cliente do sistema de gerenciamento de Banco de dados necessário para armazenar as informações do ZABBIX.
Vamos utilizar o MariaDB. 

# apt install mariadb-server mariadb-client -y

Verificar se está tufo funcionando corretamente.

# systemctl status mariadb
CTRL + c
---------------------------------------------------

Por padrão. o sistema de gerenciamento de Banco de dados vêm com a senha em branco por padrão. Precisamos criar uma senha segura. 

# mysql_secure_installation

ENTER
n
y
New Password :suasenha
Re-enter new Password:suasenha
y
y
y
y

-------------------------------------------------------------


# wget https://tinyurl.com/zabbix6refugiotech

# dpkg -i zabbix6refugiotech

# apt update -y

# apt install zabbix-server-mysql zabbix-frontend-php zabbix-apache-conf zabbix-sql-scripts zabbix-agent -y
-----------------------------------------------------------


Criar banco de dados, usuário e senha de acesso do ZABBIX. Segue os comandos:

# mysql -uroot -p

create database zabbix character set utf8mb4 collate utf8mb4_bin;
create user zabbix@localhost identified by 'SEUPASSWORD';
grant all privileges on zabbix.* to zabbix@localhost;
quit;
---------------------------------------------------------

Importação do SCHEMA para o banco de dados que criamos anteriormente.

# zcat /usr/share/doc/zabbix-sql-scripts/mysql/server.sql.gz | mysql -uzabbix -p zabbix
Obs: Após inserir a senha aguarde o retordo do prompt ! Este processo pode demorar alguns minutos para ser concluído!

Em seguida, configure o daemon do servidor Zabbix para usar o banco de dados que foi criado anteriormente.
# vim /etc/zabbix/zabbix_server.conf

Descomente #DBPassword.
Deverá ficar assim:
DBPassword=SEUPASSWORD

----------------------------------------------------------

Habilitar o fuso horário do seu servidor.

# vim /etc/zabbix/apache.conf

Descomente #php_value date.timezone Europe/Riga de
acordo com a sua região. No meu caso ficará assim:

php_value date.timezone America/Sao_Paulo
---------------------------------------------------------

Reinicie o servidor zabbix-server, zabbix-agent e apache2 para aplicar as alterações.

# systemctl restart zabbix-server zabbix-agent apache2
# systemctl enable zabbix-server zabbix-agent apache2

---------------------------------------------------------
Acesso ao frontend: http://ipdomeuservidor/zabbix
Nesse exemplo o ip do meu servidor é:
http://meu-IP/zabbix
  

Usuário padrão do ZABBIX:
Admin
Senha padrão do ZABBIX:
zabbix

---------------------------------------------------------

Obs: Possível erro de linguagem no Zabbix. Veja como resolver :

# dpkg-reconfigure locales

Selecione: 
en_US.UTF-8 UTF-8

Default locale for the system environment: en_US.UTF-8  

Reinicie o Apache
# systemctl restart apache2

Faça Logout do ZABBIX e acesse novamente !!
Pronto !!!

------------------------------------------------------------
Tunando o banco para monitoramento completo de hosts

grep MemTotal /proc/meminfo

Mosta a quantidade de memória disponivel para o servidor

cat /proc/cpuinfo | grep processor

Mostra a quantidade de nucleos ativos para o servidor

root@debian:~# mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 78
Server version: 10.5.15-MariaDB-0+deb11u1 Debian 11

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show variables like 'innodb_buffer_pool_size';
+-------------------------+-----------+
| Variable_name           | Value     |
+-------------------------+-----------+
| innodb_buffer_pool_size | 134217728 |
+-------------------------+-----------+
1 row in set (0.001 sec)

MariaDB [(none)]> show variables like 'innodb_log_file_size';
+----------------------+-----------+
| Variable_name        | Value     |
+----------------------+-----------+
| innodb_log_file_size | 100663296 |
+----------------------+-----------+
1 row in set (0.001 sec)

MariaDB [(none)]> show variables like 'innodb_flush_method';
+---------------------+-------+
| Variable_name       | Value |
+---------------------+-------+
| innodb_flush_method | fsync |
+---------------------+-------+
1 row in set (0.001 sec)

vim /etc/mysql/mariadb.conf.d/50-server.cnf

Arquivo de configuração do banco - estou usando o mariadb - Caso seja mysql, postgreSQL
-------------------------------------------------------------------


