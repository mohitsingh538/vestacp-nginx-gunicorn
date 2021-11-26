
## Deploy Django App on Vestacp - Nginx & gunicorn


### Introduction

Deploy Django application using Gunicorn on Vesta Control Panel (Ubuntu).  This does not cover creating a Django app.

#### Install
Select Nginx + php-fpm from advanced settings

![Vesta Advanced Settings](https://github.com/mohitsingh538/vestacp-nginx-gunicorn/blob/main/images/vesta_home-page.png)

#### SSH to your Vesta VM
Login as root and run this to download template files in php-fpm templates folder:
```bash
cd /usr/local/vesta/data/templates/web/nginx/php-fpm/ && \
wget https://github.com/mohitsingh538/vestacp-nginx-gunicorn/raw/main/gunicorn.zip && \
unzip gunicorn.zip && \
mv gunicorn/* . && \
rm -rf gunicorn gunicorn.zip
```

Create a file **proxy_params** inside ``/etc/nginx`` 
```bash
vi /etc/nginx/proxy_params
```
```bash
proxy_set_header Host $http_host;
proxy_set_header X-Real-IP $remote_addr;
proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
proxy_set_header X-Forwarded-Proto $scheme;
```

#### Create a folder that goes by the name of your domain
```bash
cd /home/admin/web/{your-domain.com}/private/ && \
mkdir {your-domain.com}
```
And upload your project project inside this folder. So your 
```bash
/home/admin/web/your-domain.com/private
``` 
folder should look like:
```
|-- your-domain.com
    |-- manage.py
    |-- your-app-name
	    |-- __init__.py
	    |-- settings.py
	    |-- wsgi.py
	    
    |-- templates
    |-- media
    |-- static
    |-- requirements.txt
|-- venv
|-- gunicorn.sock
```
Your ``gunicorn.sock`` file should be inside private folder

#### Install virtualenv
Create a virtual environment and name it **venv**. Naming it venv is *mandatory*.
```bash
pip3 install virtualenv
```
```bash
virtualenv venv
```
```bash
source venv/bin/activate
```

#### Download Gunicorn and Gevent

```bash
pip install gunicorn gevent
```
```bash
gunicorn --bind 0.0.0.0:8000 your-app-name.wsgi
```

You should see the following output:

```bash
[2021-06-22 11:20:02 +0000] [11820] [INFO] Starting gunicorn 20.1.0
[2021-06-22 11:20:02 +0000] [11820] [INFO] Listening at: http://0.0.0.0:8000 (11820)
[2021-06-22 11:20:02 +0000] [11820] [INFO] Using worker: sync
[2021-06-22 11:20:02 +0000] [11822] [INFO] Booting worker with pid: 11822
```
Quit the server with CONTROL-C.

Select django_nginx from the list of web templates and save

![Vesta CP Template](https://github.com/mohitsingh538/vestacp-nginx-gunicorn/raw/main/images/vesta-template.png)

Create a Systemd Service file for Gunicorn

```
vi /etc/systemd/system/gunicorn.service
```
and paste the following. Please change the path according to your domain and app name.

```bash
[Unit]
Description=gunicorn daemon
Requires=gunicorn.socket
After=network.target
[Service]
PIDFile=/run/gunicorn/pid
User=admin
Group=www-data
WorkingDirectory=/home/admin/web/your-domain.com/private/your-domain.com
ExecStart=/home/admin/web/your-domain.com/private/venv/bin/gunicorn --access-logfile - --workers 5 -k "gevent" --timeout 60 --bind unix:/home/admin/web/your-domain.com/private/gunicorn.sock you-app-name.wsgi:application
ExecReload=/bin/kill -s HUP $MAINPID
ExecStop=/bin/kill -s TERM $MAINPID
PrivateTmp=true
[Install]
WantedBy=multi-user.target
```

and then, create gunicorn.socket file

```
vi /etc/systemd/system/gunicorn.socket
```

Add:

```bash
[Unit]
Description=gunicorn socket

[Socket]
ListenStream=/home/admin/web/your-domain/private/gunicorn.sock

[Install]
WantedBy=sockets.target
```
Then,

```bash
systemctl start gunicorn
systemctl enable gunicorn
```

Finally, restart the Nginx service to apply the changes:

```
systemctl restart nginx
```

Allow permissions
```bash
chown -R admin:www-data your-domain.com
```
