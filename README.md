
## Deploy Django App on Vestacp - Nginx & gunicorn


Deploy Django application using Gunicorn on Vesta Control Panel (Ubuntu).  This does not cover creating a Django app.

#### Install Vesta Control Panel
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

#### Create a virtual enviroment
Name a virtual environment and name it **venv**. Naming it venv is *mandatory*.
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
gunicorn --bind 0.0.0.0:8000 django_project.wsgi
```

![Vesta CP Template](https://github.com/mohitsingh538/vestacp-nginx-gunicorn/raw/main/images/vesta-template.png)
