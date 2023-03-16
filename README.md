#9.2 Zabbix Часть 1 - Семериков Алексей

# Задание 1

# устанавливаем репозиторий Zabbix
wget https://repo.zabbix.com/zabbix/6.4/debian/pool/main/z/zabbix-release/zabbix-release_6.4-1+debian11_all.deb
dpkg -i zabbix-release_6.4-1+debian11_all.deb
apt update

# устанавливаем postgresql
apt install postgresql

# устанавливаем пакеты Zabbix
apt install zabbix-server-pgsql zabbix-frontend-php php7.4-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agentapt install zabbix-server-pgsql zabbix-frontend-php php7.4-pgsql zabbix-nginx-conf zabbix-sql-scripts zabbix-agent2

# создаем пользователя zabbix
sudo -u postgres createuser --pwprompt zabbix

# Создаем базу данных пользователя zabbix с названием zabbix
sudo -u postgres createdb -O zabbix zabbix

# загружаем в созданную базу zabbix скачанные скрипты sql
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix 

# редактируем файл /etc/zabbix/zabbix_server.conf 
DBPassword=password # ставим пароль от своей сзданной базы zabbix

# делаем рестарт zabbix сервера
systemctl restart zabbix-server

# настраиваем nginx в файле  /etc/zabbix/nginx.conf
раскоментируем строки:
listen  8080;
server_name  semerikovap.lan; в этой строке задали имя zabbix сервера

# перезапускаем сервисы zabbix zabbix-agent2 nginx
systemctl restart zabbix-server zabbix-agent2 nginx php7.4-fpm

# добавляем эти сервисы в автозагрузку
systemctl enable zabbix-server zabbix-agent2 nginx php7.4-fpm

# Задание 2

# с официального сайта устанавливаем zabbbix-agent2 на оба новых хоста
wget https://repo.zabbix.com/zabbix/6.4/debian/pool/main/z/zabbix-release/zabbix-release_6.4-1+debian11_all.deb
dpkg -i zabbix-release_6.4-1+debian11_all.deb
apt update
apt install zabbix-agent2 zabbix-agent2-plugin-*

# перезагружаем агента на обоих хостах и проверяем статус а так же добавляем их в автозагрузку
systemctl restart zabbix-agent2
systemctl status zabbix-agent2
systemctl enable zabbix-agent2

# подключаемся к потоку логов на каждом хосте
tail -f /var/log/zabbix/zabbix_agent2.log

# прописываем сервер на хостах
 nano /etc/zabbix/zabbix_agent2.conf

# перезагружем агента на обоих хостах
systemctl restart zabbix-agent2

# снова подключаемся к логам и проверяем что все рабоотает
tail -f /var/log/zabbix/zabbix_agent2.log


