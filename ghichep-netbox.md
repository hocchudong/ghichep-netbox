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
	cp /opt/netbox/netbox/netbox/configuration.example.py /opt/netbox/netbox/netbox/configuration.py 
	```

- Thực thi file dưới và chép kết quả hiển thị để dùng cho khai báo dưới 

    ```sh
    /opt/netbox/netbox/generate_secret_key.py
    ```
	
- Mở file `/opt/netbox/netbox/netbox/configuration.py` và sửa theo hướng dẫn dưới. Sửa trong 03 dòng dưới.

    ```sh
    ALLOWED_HOSTS
    DATABASE
    SECRET_KEY
    ```

    - Khai báo dòng `ALLOWED_HOSTS`
    
        ```sh
        ALLOWED_HOSTS = ['dia_chi_ip_cua_netbox_server']
        ```
        
    - Khai báo `DATABASE`
    
        ```sh 
        DATABASE = {
            'NAME': 'netbox',        
            'USER': 'netbox',               
            'PASSWORD': 'DATA@!2o016',           
            'HOST': 'localhost',     
            'PORT': '',               
        }
        ```
        
    - Sử dụng output ở lúc thực thi file `/opt/netbox/netbox/generate_secret_key.py` để điền vào dòng dưới
    
        ```sh
        SECRET_KEY = 'A2D8l^3-Ev_SFDLZvNdDThZdT90TZ-beEvlI=boawc@_LEC5wA'
        ```

- Lưu và thoát

- Đồng bộ DB cho `netbox`

    ```sh
    cd /opt/netbox/netbox/
    ./manage.py migrate 
    ```
    
    - Kết quả tương tự như dưới
    
        ```sh
        root@vnpt-netbox:/opt/netbox/netbox# ./manage.py migrate
        Operations to perform:
          Apply all migrations: admin, auth, circuits, contenttypes, dcim, extras, ipam, secrets, sessions, tenancy
        Running migrations:
          Rendering model states... DONE
          Applying contenttypes.0001_initial... OK
          Applying auth.0001_initial... OK
          Applying admin.0001_initial... OK
          Applying admin.0002_logentry_remove_auto_add... OK
          Applying contenttypes.0002_remove_content_type_name... OK
          Applying auth.0002_alter_permission_name_max_length... OK
          Applying auth.0003_alter_user_email_max_length... OK
          Applying auth.0004_alter_user_username_opts... OK
          Applying auth.0005_alter_user_last_login_null... OK

        ```
             
- Tạo Super User

    ```sh
    ./manage.py createsuperuser
    ```
    
    - Nhập các thông số theo yêu cầu, tương tự như dưới
    
        ```sh 
        root@vnpt-netbox:/opt/netbox/netbox# ./manage.py createsuperuser
        Username (leave blank to use 'root'): admin
        Email address: tcvn1985@gmail.com
        Password:
        Password (again):
        Superuser created successfully.
        root@vnpt-netbox:/opt/netbox/netbox#
        root@vnpt-netbox:/opt/netbox/netbox#
        root@vnpt-netbox:/opt/netbox/netbox#
        ```
        
-  Collect Static Files

    ```sh
    ./manage.py collectstatic
    ```
    
- Kiểm tra ứng dụng

    ```sh
    ./manage.py runserver 172.16.69.27:8000 --insecure
    ```
    
    - Kết quả như dưới là thành công rồi nhé.
    
        ```sh

        erforming system checks...

        System check identified no issues (0 silenced).
        October 16, 2016 - 05:14:28
        Django version 1.10, using settings 'netbox.settings'
        Starting development server at http://0.0.0.0:8000/
        Quit the server with CONTROL-C.
        ```
            
#### Cài đặt WEB SERVER cho NetBox

- Cài đặt `gunicorn`

    ```sh
    apt-get install -y gunicorn supervisor
    ```

- Cài đặt `apache2`

    ```
    apt-get install -y apache2
    ```

- Tạo file `/etc/apache2/sites-available/netbox.conf` với nội dung dưới.
 - Lưu ý dòng: `ServerName 172.16.69.100` (địa chỉ IP của máy `NETBOX`)

    ```sh
    <VirtualHost *:80>
        ProxyPreserveHost On

        ServerName 172.16.69.100

        Alias /static /opt/netbox/netbox/static

        <Directory /opt/netbox/netbox/static>
            Options Indexes FollowSymLinks MultiViews
            AllowOverride None
            Require all granted
        </Directory>

        <Location /static>
            ProxyPass !
        </Location>

        ProxyPass / http://127.0.0.1:8001/
        ProxyPassReverse / http://127.0.0.1:8001/
    </VirtualHost>
    ```
	
- Chạy các lệnh dưới

    ```sh
    a2enmod proxy
    a2enmod proxy_http
    a2ensite netbox
    service apache2 restart
    ```

- Tạo file `/opt/netbox/gunicorn_config.py`

    ```sh
    command = '/usr/bin/gunicorn'
    pythonpath = '/opt/netbox/netbox'
    bind = '127.0.0.1:8001'
    workers = 3
    user = 'www-data'
    ```
    
- Tạo file `/etc/supervisor/conf.d/netbox.conf`

    ```sh
    [program:netbox]
    command = gunicorn -c /opt/netbox/gunicorn_config.py netbox.wsgi
    directory = /opt/netbox/netbox/
    user = www-data
    ```

Welcome123

Hcd@2016

	
	
	
	
	
	