# DevOps Guide: Running a Python Backend on a Fresh Ubuntu Machine

## Scenario

You have:

- A fresh Ubuntu machine
- No Python packages installed (or only the system Python)
- No virtual environment
- Only the backend source code
- Need to verify the application before containerizing it

---

# Step 1: Get the Source Code

Clone the repository.

```bash
git clone https://github.com/company/backend.git
```

Move into the project.

```bash
cd backend
```

---

# Step 2: Inspect the Project Structure

Run:

```bash
ls -la
```

Common structures:

### Flask

```text
backend/
├── app.py
├── requirements.txt
├── .env.example
└── README.md
```

### FastAPI

```text
backend/
├── main.py
├── requirements.txt
├── app/
└── README.md
```

### Django

```text
backend/
├── manage.py
├── requirements.txt
├── project/
└── README.md
```

Things to identify:

- requirements.txt
- pyproject.toml
- Pipfile
- poetry.lock
- README.md

---

# Step 3: Read README

Always start here.

```bash
cat README.md
```

Look for:

- Python version
- Installation instructions
- Required services
- Environment variables
- Startup command

---

# Step 4: Determine Python Version

Check:

```bash
cat .python-version
```

or

```toml
pyproject.toml
```

or

```text
README.md
```

Example:

```text
Python 3.11
```

Never assume.

---

# Step 5: Install Python

Update Ubuntu.

```bash
sudo apt update
```

Install Python.

```bash
sudo apt install -y python3 python3-pip python3-venv
```

Verify.

```bash
python3 --version
```

```bash
pip3 --version
```

---

# Step 6: Create Virtual Environment

Never install project packages globally.

Create virtual environment.

```bash
python3 -m venv venv
```

Activate it.

```bash
source venv/bin/activate
```

Prompt changes:

```text
(venv) ubuntu@server:~/backend$
```

Verify.

```bash
which python
```

Should show:

```text
backend/venv/bin/python
```

---

# Step 7: Identify Dependency Manager

Most common:

### requirements.txt

```text
Flask
gunicorn
requests
sqlalchemy
```

Install:

```bash
pip install -r requirements.txt
```

---

### Poetry

Files:

```text
pyproject.toml
poetry.lock
```

Install Poetry.

```bash
pip install poetry
```

Install packages.

```bash
poetry install
```

---

### Pipenv

Files:

```text
Pipfile
Pipfile.lock
```

Install.

```bash
pip install pipenv
```

Install packages.

```bash
pipenv install
```

---

# Step 8: Configure Environment Variables

Look for:

```text
.env.example
```

Copy.

```bash
cp .env.example .env
```

Edit.

```bash
nano .env
```

Example.

```text
SECRET_KEY=abcd123

DATABASE_URL=postgres://...

REDIS_URL=redis://...

DEBUG=False
```

---

# Step 9: Identify External Services

Inspect requirements.

| Package | Service |
|----------|----------|
| psycopg2 | PostgreSQL |
| pymysql | MySQL |
| redis | Redis |
| boto3 | AWS |
| pika | RabbitMQ |
| kafka-python | Kafka |
| pymongo | MongoDB |

Make sure these services are available.

---

# Step 10: Identify Framework

## Flask

Look for:

```python
from flask import Flask
```

Usually started with:

```bash
python app.py
```

or

```bash
flask run
```

---

## FastAPI

Look for:

```python
from fastapi import FastAPI
```

Usually started with:

```bash
uvicorn main:app
```

or

```bash
uvicorn app.main:app
```

---

## Django

Look for:

```text
manage.py
```

Run:

```bash
python manage.py runserver
```

---

# Step 11: Database Migration

Many applications require schema creation.

### Django

```bash
python manage.py migrate
```

---

### Flask (Alembic)

```bash
flask db upgrade
```

---

### Generic Alembic

```bash
alembic upgrade head
```

---

# Step 12: Start the Application

### Flask

```bash
python app.py
```

or

```bash
flask run
```

---

### FastAPI

```bash
uvicorn main:app
```

---

### Django

```bash
python manage.py runserver
```

---

# Step 13: Verify It Is Running

Check processes.

```bash
ps -ef | grep python
```

Check listening ports.

```bash
ss -tulpn
```

Example:

```text
0.0.0.0:8000
```

---

# Step 14: Test Endpoints

Example:

```bash
curl http://localhost:8000
```

Health endpoint:

```bash
curl http://localhost:8000/health
```

---

# Step 15: Run Tests

Common commands.

```bash
pytest
```

or

```bash
python -m pytest
```

or

```bash
python manage.py test
```

---

# Step 16: Troubleshooting

## Missing Module

Example:

```text
ModuleNotFoundError
```

Solution:

```bash
pip install -r requirements.txt
```

---

## Virtual Environment Not Active

Check.

```bash
which python
```

Should point to:

```text
venv/bin/python
```

---

## Port Already in Use

Find process.

```bash
sudo lsof -i :8000
```

Kill.

```bash
kill -9 <PID>
```

---

## Database Connection Failed

Check:

- Database running
- Credentials
- Firewall
- Network
- Environment variables

---

## Migration Missing

Example:

```text
relation does not exist
```

Run migrations.

```bash
python manage.py migrate
```

---

# Step 17: Production Server

Never use development servers in production.

Use:

### Flask

```bash
gunicorn app:app
```

---

### FastAPI

```bash
gunicorn -k uvicorn.workers.UvicornWorker main:app
```

or

```bash
uvicorn main:app --host 0.0.0.0 --port 8000
```

---

### Django

```bash
gunicorn project.wsgi
```

---

# Step 18: Containerize

Once verified:

```text
Clone Repository
        ↓
Install Python
        ↓
Create Virtual Environment
        ↓
Install Dependencies
        ↓
Configure .env
        ↓
Connect Database
        ↓
Run Migrations
        ↓
Start Application
        ↓
Verify Health
        ↓
Dockerize
        ↓
Deploy
```

---

# DevOps Checklist

- [ ] Clone repository
- [ ] Read README
- [ ] Identify Python version
- [ ] Install Python and pip
- [ ] Create virtual environment (`venv`)
- [ ] Activate virtual environment
- [ ] Install dependencies (`pip install -r requirements.txt` or Poetry/Pipenv)
- [ ] Create `.env`
- [ ] Configure database/cache/message broker
- [ ] Run database migrations
- [ ] Start application
- [ ] Verify logs
- [ ] Verify listening port
- [ ] Test application endpoints
- [ ] Run automated tests (`pytest` or framework-specific tests)
- [ ] Use Gunicorn/Uvicorn for production
- [ ] Proceed to Docker containerization

---

# Things a DevOps Engineer Should Look For

| File | Purpose |
|------|---------|
| `requirements.txt` | Python dependencies |
| `pyproject.toml` | Modern dependency management (Poetry) |
| `Pipfile` | Pipenv dependencies |
| `.python-version` | Required Python version |
| `.env.example` | Required environment variables |
| `manage.py` | Django management commands |
| `alembic.ini` | Database migrations |
| `Dockerfile` | Existing containerization |
| `docker-compose.yml` | Local services (DB, Redis, etc.) |
| `README.md` | Setup and run instructions |
