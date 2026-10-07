# Deploy to PythonAnywhere

This project is configured for PythonAnywhere with Django 6.0.3 and Python 3.13.
The portfolio uses Django's SQLite database; uploaded/media files and a separate
database server are not currently needed.

## 1. Create the app files on PythonAnywhere

Sign in to PythonAnywhere and open **Consoles → Bash**. Run:

```bash
git clone https://github.com/00dep/portfolio.git
cd portfolio
mkvirtualenv --python=/usr/bin/python3.13 portfolio-venv
pip install -r requirements.txt
python manage.py migrate
python manage.py collectstatic --noinput
```

## 2. Create the web app

In **Web → Add a new web app**, select **Manual configuration** and Python 3.13.
Set the virtualenv to:

```text
/home/<your-pythonanywhere-username>/.virtualenvs/portfolio-venv
```

Set both **Source code** and **Working directory** to:

```text
/home/<your-pythonanywhere-username>/portfolio
```

Replace `<your-pythonanywhere-username>` with the username shown in your
PythonAnywhere account. The site address will be
`<your-pythonanywhere-username>.pythonanywhere.com`.

## 3. Configure the WSGI file

In the **Web** tab, open the WSGI configuration file linked under **Code**.
Replace its contents with the following, substituting your PythonAnywhere
username and a newly generated secret key:

```python
import os
import sys

path = '/home/<your-pythonanywhere-username>/portfolio'
if path not in sys.path:
    sys.path.insert(0, path)

os.environ['DJANGO_SETTINGS_MODULE'] = 'portfolio.settings'
os.environ['DJANGO_DEBUG'] = 'False'
os.environ['DJANGO_ALLOWED_HOSTS'] = '<your-pythonanywhere-username>.pythonanywhere.com'
os.environ['DJANGO_SECRET_KEY'] = '<paste-a-new-secret-key-here>'

from django.core.wsgi import get_wsgi_application
application = get_wsgi_application()
```

Generate the key in the Bash console with:

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Keep this secret only in the PythonAnywhere WSGI file; do not put it in GitHub.

## 4. Configure static files

In the **Web** tab's **Static files** section, add this mapping:

| URL | Directory |
| --- | --- |
| `/static/` | `/home/<your-pythonanywhere-username>/portfolio/staticfiles` |

Click **Reload** in the Web tab, then open your `pythonanywhere.com` site.

## Updating the site

After pushing code changes to GitHub, run these commands in a PythonAnywhere
Bash console:

```bash
cd ~/portfolio
git pull
workon portfolio-venv
pip install -r requirements.txt
python manage.py migrate
python manage.py collectstatic --noinput
```

Then click **Reload** in the PythonAnywhere Web tab. If the site errors, check
the error log linked from that tab.
