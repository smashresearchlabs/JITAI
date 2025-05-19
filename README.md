# JITAI Gambling Risk MitiGators
CIS4914 - Senior Project

Sponsored by Dr. Yonghwan Chang

This application tracks and analyzes real-time thoughts and sentiments shared by sports bettors on social media platforms. It provides timely notifications promoting healthy behaviors, offering emotional support, and presenting instant resolutions to help users maintain well-being and healthy life.

## Table of Contents

- [JITAI Gambling Risk MitiGators](#jitai-gambling-risk-mitigators)
  - [Table of Contents](#table-of-contents)
  - [Repo Organization](#repo-organization)
  - [Developer Setup (Backend)](#developer-setup-backend)
  - [Developer Setup (Frontend)](#developer-setup-frontend)
  - [Firebase Setup](#firebase-setup)
  - [Continuous Integration](#continuous-integration)
  - [Miscellaneous](#miscellaneous)

## Repo Organization

JITAIGamblingRiskMitiGators is organized into two primary directories: **JITAIGamblingRiskMitiGators-Django** for the Django-powered backend code, and **JITAIGamblingRiskMitiGators-RN** for the React Native frontend code.

## Developer Setup (Backend)

1. Clone the repo

```bash
git clone https://github.com/jonathanwuki/JITAI-Sports-Gambling-Risk-MitiGators.git
```

2. Install Python and Pip
   - Download the latest version of Python from https://www.python.org/downloads/ 
   - Ensure the latest version of pip is installed by running `python3 -m ensurepip –upgrade` in a terminal

3. Initialize a virtual environment (venv) in the root directory

```bash
python3 -m venv .venv
source .venv/bin/activate
```

4. Copy `.env-EXAMPLE` to `.env`

```bash
cp .env-EXAMPLE .env
```

> [!NOTE]
> Make sure to get your own CFBD API key (`COLLEGE_FOOTBALL_API_KEY`) and set it in your newly-created `.env` file. You can get a key for free [here](https://collegefootballdata.com/key).

5. Install Django

```bash
python3 -m pip install Django
```

6. Install PostgreSQL and pgAdmin 4
   - Mac:
     - `brew install postgresql`
     - `brew install --cask pgadmin4`
   - Windows: see [here](https://www.postgresql.org/download/windows/)

7. Configure pgAdmin 4
   - Go to Servers > Login > Group Roles. Right click and create a new role. Set the priviledges to allow log in
   - Name your role and set the password
   - Right click Databases and create "jitaigamblingriskmitigators", and set yourself as the owner

8. Install `psycopg2`

```bash
python3 -m pip install psycopg2
```

9. Install all other required packages

```bash
python3 -m pip install -r requirements.txt
```

10. Run the following commands in the JITAIGamblingRiskMitiGators-Django directory:

```bash
python3 manage.py migrate
python3 manage.py createsuperuser
```

11. Test to make sure the server runs with the following command:

```bash
python3 manage.py runserver
```

> At this point, the admin dashboard should be visible at http://localhost:8000/admin after logging in with your newly-created superuser credentials.

12. Set up ngrok
   - Follow steps 1-4 of [these instructions](https://ngrok.com/docs/getting-started)
   - Navigate to [`JITAIGamblingRiskMitiGators-Django/project/settings.py`](JITAIGamblingRiskMitiGators-Django/project/settings.py) and add your new static domain to `ALLOWED_HOSTS` and `CSRF_TRUSTED_ORIGIN`
   - Navigate to [`JITAIGamblingRiskMitiGators-RN/constants/AppUrls.ts`](JITAIGamblingRiskMitiGators-RN/constants/AppUrls.ts) and add your new static domain to the list. Comment out other URLs in the list. This will ensure the frontend uses this host for API calls

13. Run ngrok

In a terminal, navigate to the **JITAIGamblingRiskMitiGators-Django** directory and run `ngrok http 8000 --url <your new static domain>`.

14. Run the server

In a terminal, navigate to the **JITAIGamblingRiskMitiGators-Django** directory and run `python manage.py runserver 0.0.0.0:8000`.

Now you can open the following links in your browser to view the index of entities and manage your Django app as an admin:
- Index: http://localhost:8000
- Django admin view: http://localhost:8000/admin

> [!NOTE]
> When running the backend locally, you will be using a local instance of a PostgreSQL database. Data stored here will differ than the one used by Heroku.

15. Run the Redis server
   - Mac: `brew services start redis`
   - Windows: Open a terminal as admin (you’ll need root privileges) and navigate to the directory where Redis is installed (i.e. `C:\Program Files\Redis`). Run `redis-server` in the terminal.

> [!WARNING]
> You may get this known issue in Windows when running the command: `# Creating Server TCP listening socket *:6379: No such file or directory`. If this happens, run `redis-cli.exe` from the Redis directory, then run `shutdown` while that is executing. Run `Ctrl+C` to exit the executable, then re-run  `redis-server` again and it should work.

16. Run the Celery worker
   - Open a new terminal and navigate to the **JITAIGamblingRiskMitiGators-Django** directory
   - Reactivate the virtual environment if it is not already active
   - Start the Celery worker by running `celery -A project worker --pool=solo -l info`
      - This command starts the Celery worker, which polls Redis for new tasks
      - The `--pool=solo` flag forces a single threaded process for compatibility with Windows

17. Run the Celery beat scheduler
   - Open a new terminal and navigate to the **JITAIGamblingRiskMitiGators-Django** directory
   - Reactivate the virtual environment if it is not already active
   - Start the celery beat scheduler by running `celery -A project beat --loglevel=info`
      - This command starts the Celery beat scheduler, which sends tasks to the message broker (in this case, Redis) at set intervals, e.g., every 10 seconds

Now the backend should be up and running locally!

## Developer Setup (Frontend)

1. Navigate to the **JITAIGamblingRiskMitiGators-RN** directory

2. Install dependencies

```bash
npm install
```

3. Start the app

```bash
npx expo start
```

## Firebase Setup

This project uses Firebase Authentication to handle user authentication as well as Firestore to store user data. Note that the Firebase configuration files have not been included in this repo for security reasons. When attempting to run this project locally for the first time, ask Jonathan for the configuration files.

## Continuous Integration

This project uses [Harness](https://www.harness.io/) for Continuous Integration/Continuous Deployment (CI/CD). Every time a commit is pushed, both the frontend and backend will be built and linted. In the future, this will be expanded to support automatic deployments to Heroku.

## Miscellaneous

Database entities for User, UserData, and NotificationData are defined in [`JITAIGamblingRiskMitiGators-Django/app/models.py`](JITAIGamblingRiskMitiGators-Django/app/models.py).

APIs are defined in the backend in [`JITAIGamblingRiskMitiGators-Django/app/views.py`](JITAIGamblingRiskMitiGators-Django/app/views.py).

API URLs are captured in [`JITAIGamblingRiskMitiGators-Django/project/urls.py`](JITAIGamblingRiskMitiGators-Django/project/urls.py).

These REST APIs are documented on [Swagger](http://localhost:8000/swagger/).