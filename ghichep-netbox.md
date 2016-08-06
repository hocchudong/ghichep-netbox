# Ghi chép cài đặt Netbox

- Các ghi chép khi cài đặt ứng dụng NetBox
- Tham khảo:
 - http://netbox.readthedocs.io/
 - https://github.com/digitalocean/netbox
 
 
##  Bước chuẩn bị
- Môi trường cài đặt:
	- Distro
	
		```sh
		root@c-u14-ctl1:~# lsb_release -a
		No LSB modules are available.
		Distributor ID: Ubuntu
		Description:    Ubuntu 14.04.3 LTS
		Release:        14.04
		Codename:       trusty
		```

	- Network: 
		
		```sh
		eth0: 172.16.69.221 | 255.255.255 | 172.16.69.1
		```
	
## Cài đặt database

- Cài đặt postgresql

	```sh
	sudo apt-get install -y postgresql libpq-dev python-psycopg2
	```

- Cấu hình postgresql sau khi cài đăt

	```sh
	# sudo -u postgres psql
	psql (9.3.13)
	Type "help" for help.

	postgres=# CREATE DATABASE netbox;
	CREATE DATABASE
	postgres=# CREATE USER netbox WITH PASSWORD 'J5brHrAXFLQSif0K';
	CREATE ROLE
	postgres=# GRANT ALL PRIVILEGES ON DATABASE netbox TO netbox;
	GRANT
	postgres=# \q
	```
	
	