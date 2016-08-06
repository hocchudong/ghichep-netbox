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
		eth0: 
			IP: 172.16.69.221 
			Subnetmask: 255.255.255 
			Gateway: 172.16.69.1
		```
	
## Cài đặt database

- Cài đặt postgresql

	```sh
	sudo apt-get install -y postgresql libpq-dev python-psycopg2
	```

- Cấu hình postgresql sau khi cài đăt

	```sh
	sudo -u postgres psql
	CREATE DATABASE netbox;
	CREATE USER netbox WITH PASSWORD 'Hcd@2016';
	GRANT ALL PRIVILEGES ON DATABASE netbox TO netbox;
	\q
	```
	
## Cài đặt NetBox
- Cài đặt các gói cần thiết cho NetBox
	
	```sh
	sudo apt-get install -y python2.7 python-dev git python-pip libxml2-dev libxslt1-dev libffi-dev graphviz libpq-dev
	```

- Tạo thư mục

	```sh
	mkdir -p /opt/netbox/
	cd /opt/netbox/
	```
	
- Tải NetBox

	```sh
	git clone -b master https://github.com/digitalocean/netbox.git  .
	```
	
- Cài đặt 
	
	```sh
	sudo pip install -r requirements.txt
	```
	
- Copy file cấu hình 

	```sh
	cd netbox/netbox/
	cp configuration.example.py configuration.py
	```
	
- Sửa các dòng trong file ` ... ` như sau

```sh
ALLOWED_HOSTS
DATABASE
SECRET_KEY
```

- Tiếp
	
	
	
	
	
	
	
	
Welcome123
Hcd@2016

	
	
	
	
	
	