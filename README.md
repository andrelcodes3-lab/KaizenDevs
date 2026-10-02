# Kaizen Devs

Kaizen Devs is a community chat application for developers. Members can join topic rooms, exchange messages in real time, and update their profile details and picture.

**Live site:** [chat-project-nqzo.onrender.com](https://chat-project-nqzo.onrender.com/)

## Features

- Browse chat rooms and join conversations
- Send and receive messages live over WebSockets
- Sign up and log in with Django Allauth
- Save room messages to the database
- Update your name and profile picture
- Manage rooms and messages through Django admin

## Built with

- Python and Django
- Django Channels and Daphne for WebSockets and ASGI
- Redis as the Channels layer in deployment
- PostgreSQL on Render (SQLite is the local default)
- HTMX for live message updates
- Tailwind CSS and daisyUI for styling

## Run locally

### Requirements

- Python 3.12 (see `.python-version`)
- Redis, for the WebSocket channel layer

### Setup

1. Clone the repository and enter the project directory.
2. Create and activate a virtual environment:

   ```bash
   python -m venv .venv
   # macOS/Linux
   source .venv/bin/activate
   # Windows PowerShell
   .venv\Scripts\Activate.ps1
   ```

3. Install the Python dependencies and apply database migrations:

   ```bash
   pip install -r requirements.txt
   python manage.py migrate
   ```

4. Start Redis locally. The development settings expect it at `127.0.0.1:6379` unless `REDIS_URL` is set.
5. Create an account at the signup page, or create an admin account and add rooms in the admin interface:

   ```bash
   python manage.py createsuperuser
   ```

6. Start the ASGI server:

   ```bash
   python -m daphne -b 127.0.0.1 -p 8000 chat_project.asgi:application
   ```

   Visit [http://127.0.0.1:8000](http://127.0.0.1:8000). The admin interface is at `/admin/`.

## Configuration

The app reads these environment variables:

| Variable | Purpose |
| --- | --- |
| `SECRET_KEY` | Django secret key. Set a secure value outside local development. |
| `DATABASE_URL` | Optional database connection URL. If unset, Django uses `db.sqlite3`. |
| `REDIS_URL` | Redis connection URL for Django Channels. Locally defaults to `127.0.0.1:6379`. |
| `RENDER_EXTERNAL_HOSTNAME` | Set by Render; used to configure the allowed host and trusted HTTPS origin. |
| `AWS_STORAGE_BUCKET_NAME`, `AWS_S3_REGION_NAME`, `AWS_S3_ACCESS_KEY_ID`, `AWS_S3_SECRET_ACCESS_KEY` | S3 storage configuration for uploaded profile pictures. The bucket name and region have defaults in settings; provide credentials for uploads. |

## Deployment

The repository includes a Render Blueprint in `render.yaml`. It provisions a Django web service, PostgreSQL database, and Redis-compatible key-value service. The build script installs Python dependencies, collects static files, and runs migrations. Daphne serves the ASGI application so WebSocket connections are supported.

The deployed configuration uses Amazon S3 for uploaded media. Configure valid AWS credentials and the target bucket for profile picture uploads.

## Project layout

```text
chat_project/   Django project settings, URLs, and ASGI entry point
core/           Chat app models, views, WebSocket consumer, and templates
assets/         Source styles
static/         Served CSS and JavaScript assets
render.yaml     Render service and database configuration
build.sh        Render build steps
```
