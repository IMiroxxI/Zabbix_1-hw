# Домашнее задание к занятию "`Система мониторинга Zabbix`" - `Khalatov Alexandr`

### Задание 1

Установите Zabbix Server с веб-интерфейсом.
Для установки нам необходимо воспользоваться конфигуратором на сайте Zabbix для ОС Ubuntu.
[Zabbix.com](https://www.zabbix.com/ru/download?zabbix=7.0&os_distribution=ubuntu&os_version=24.04&components=server_frontend_agent&db=pgsql&ws=apache)

Перед началом установки Zabbix необходимо установить базу данных Postgresql `sudo apt-get install postgresql`.
Далее переходим к установке Zabbix.
*Переходим под суперпользователя*
```
sudo -s
```
*Установка репозитория Zabbix*
```
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
apt update
```
*Установка Zabbix сервера, веб-интерфейса и агента.*
```
apt install zabbix-server-pgsql zabbix-frontend-php php8.3-pgsql zabbix-apache-conf zabbix-sql-scripts zabbix-agent
```
*Создаем базу данных*
```
sudo -u postgres createuser --pwprompt zabbix
sudo -u postgres createdb -O zabbix zabbix
```
*На хосте Zabbix сервера импортируем начальную схему и данные. Вам будет предложено ввести недавно созданный пароль.*
```
zcat /usr/share/zabbix-sql-scripts/postgresql/server.sql.gz | sudo -u zabbix psql zabbix
```
*Настраиваем базу данных сервера. Необходимо отредактировать файл /etc/zabbix/zabbix_server.conf*
Находим строчку `DBPassword=` и вписываем наш пароль который создавали на шаге sudo -u postgres createuser --pwprompt zabbix
*Запускаем процессы Zabbix сервера и агента*
```
systemctl restart zabbix-server zabbix-agent apache2
systemctl enable zabbix-server zabbix-agent apache2
```
И в конце проверяем Zabbix через web-интерфейс.
`http://Ваш IP_Zabbix сервера/zabbix`
![1](https://github.com/IMiroxxI/Zabbix_1-hw/blob/main/img/1.png)

---

### Задание 2

Установите Zabbix Agent на два хоста.

Процесс установки:
Я создал дополнительные 2 ВМ на yandex cloud - vm-2 и vm-3.
Далее переходим на сайт [Zabbix](https://www.zabbix.com/ru/download?zabbix=7.0&os_distribution=ubuntu&os_version=24.04&components=agent&db=&ws=) и находим инструкцию по установке агента.
И устанавливаем агенты на 2 ВМ.
*Переходим под суперпользователя*
```
sudo -s
```
*Установка репозитория Zabbix*
```
wget https://repo.zabbix.com/zabbix/7.0/ubuntu/pool/main/z/zabbix-release/zabbix-release_latest_7.0+ubuntu24.04_all.deb
dpkg -i zabbix-release_latest_7.0+ubuntu24.04_all.deb
apt update
```
*Установка Zabbix агента.*
```
apt install zabbix-agent
```
*Запускаем процесс Zabbix агента*
```
systemctl restart zabbix-agent
systemctl enable zabbix-agent
```
Добавляем агенты в Zabbix сервер в web-интерфейсе на вкладке Data collection/Hosts.
Производим соответвующую настройку, прописываем Host name, Groups и Template.
Заходим на vm-2 и vm-3 и в файле `sudo nano /etc/zabbix/zabbix_agentd.conf` меняем адрес Zabbix сервера с `Server=127.0.0.1` на `Server=наш адрес сервера`.
Проверяем подключение агентов в web-интерфейсе сервера.
![2](https://github.com/IMiroxxI/Zabbix_1-hw/blob/main/img/2.png)
Проверям логи на каждом из агентов `sudo tail -f /var/log/zabbix/zabbix_agentd.log`. 
![2-1](https://github.com/IMiroxxI/Zabbix_1-hw/blob/main/img/2-1.png)
![2-2](https://github.com/IMiroxxI/Zabbix_1-hw/blob/main/img/2-2.png)
Проверяем раздел Monitoring > Latest data для обоих хостов, где видны поступающие от агентов данные.
![2-3](https://github.com/IMiroxxI/Zabbix_1-hw/blob/main/img/2-3.png)

---


