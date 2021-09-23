
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
wget https://github.com/mohitsingh538/vestacp-nginx-gunicorn/raw/main/gunicorn.zip
unzip gunicorn.zip && \
mv gunicorn/* . && \
rm -rf gunicorn gunicorn.zip
```
#### Create a folder that goes by the name of your domain
```bash
cd /home/admin/web/{your-domain.com}/private/ && \
mkdir {your-domain.com}
```
And upload your project project inside this folder. So your ``` /home/admin/web/your-domain.com/private/ ``` folder should look like:
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

#### Download Gunicorn

```bash
pip install gunicorn
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

Finally, restart the Nginx service to apply the changes:

```
systemctl restart nginx
```

Allow permissions
```bash
chown -R admin:www-data your-domain.com
```
