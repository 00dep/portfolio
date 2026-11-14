# Deploy to Vercel

This Django project can be deployed from its Git repository or with the Vercel
CLI. Vercel detects `manage.py` and `portfolio/wsgi.py`; it also runs
`collectstatic` and serves collected static files from its CDN.

## 1. Import the project

1. Import this repository in Vercel, or install the Vercel CLI and run
   `vercel` from the project root to link it.
2. Keep the project root as the **Root Directory**.
3. Use the detected Django/Python framework preset and default build settings.
   No custom output directory or build command is required.

The committed `.python-version` selects Python 3.13.

## 2. Add environment variables

In **Project > Settings > Environment Variables**, set:

| Name | Value |
| --- | --- |
| `DJANGO_SECRET_KEY` | A newly generated, private Django secret |
| `DJANGO_DEBUG` | `False` |
| `DJANGO_ALLOWED_HOSTS` | Optional comma-separated custom hostnames, such as `portfolio.example.com` |
| `DATABASE_URL` | A PostgreSQL connection URL from a hosted PostgreSQL provider |

Generate a secret locally with:

```bash
python -c "from django.core.management.utils import get_random_secret_key; print(get_random_secret_key())"
```

Set the variables for **Production**, and for **Preview** too if preview
deployments need to use the app or database. Do not commit these values or put
them in source code. Vercel's `.vercel.app` hostnames are already allowed;
custom domains should be added to `DJANGO_ALLOWED_HOSTS`.
If Vercel starts an initial build before these variables are available, that
build may fail because production requires `DJANGO_SECRET_KEY`; add the values
and redeploy.

## 3. Set up the database

SQLite is retained for local development. Vercel function filesystems are
ephemeral, so SQLite data is not a durable production database. Create a
PostgreSQL database with a provider such as Neon or Supabase and set its
connection URL as `DATABASE_URL` in Vercel.

After setting `DATABASE_URL` in a trusted local environment, run:

```bash
python manage.py migrate
python manage.py createsuperuser
```

Run migrations again when future code adds or changes models. Do not run
migrations in every function invocation.

## 4. Deploy and verify

Push to the connected Git branch or run:

```bash
vercel --prod
```

Open the deployment URL and verify the portfolio and its static assets. If
using Django admin, visit `/admin/` after migrating the database and creating a
superuser. Check the Vercel deployment logs if the build or function fails.
