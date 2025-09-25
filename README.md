# devops-notes-app
A small FastAPI web service to practise DevOps fundamentals (code → container → CI/CD → docs).

---

## Prerequisites
- Python 3.11 or newer
- Git
- (Optional) VS Code



Setup (Windows PowerShell)

## Step 1 — Navigate to your project folder in powershell
```
cd C:\Users\<YourUsername>\Projects
mkdir devops-notes-app
cd devops-notes-app
```


## Step 2 — Create project structure (folders & files)

Set up a clean scaffold for the project so code, tests, and documentation are organized from the start.

#### create subfolders
```
mkdir app
mkdir tests
mkdir docs
```

### create empty files

#### Resulting structure

```
devops-notes-app/
  app/
    main.py
  tests/
    test_health.py
  docs/
    architecture.md
    runbook.md
    learning-log.md
  .gitignore
  README.md
  requirements.txt
```
---

## Step 3 — Install dependencies

This project uses three Python packages:

- **FastAPI** — framework for building the API routes.  
- **Uvicorn** — web server to run the FastAPI app.  
- **Pytest** — testing framework for automated checks.  


####  First-time setup (when starting this project from scratch)
Use this when you first create the project and `requirements.txt` is still empty:

```
pip install fastapi uvicorn pytest
pip freeze > requirements.txt
```

#### Future setups (when requirements.txt already exists)
Use this when coming back to the project later, or after cloning it from GitHub:

```
pip install -r requirements.txt
```
THIS INSTALLS THE EXACT VERSIONS LISTED IN THE FILE, ENSURING THE ENVIRONMENT IS IDENTICAL ACROSS MACHINES


---

## Step 4 — Initialize Git and push to GitHub

#### Initialize Git in your project folder

```
git init
```
#### Stage all files
```
git add .
```
#### Make your first commit
```
git commit -m "Initial commit: project scaffold + README"
```
#### Connect to your GitHub repository
```
git remote add origin https://github.com/<your-username>/<repo-name>.git
```
#### Push to GitHub
```
git branch -M main
git push -u origin main
```

---

## Step 5 — Add the FastAPI app and run it on port 8000

In this step you’ll create the first working version of your web service.

#### Open the `main.py` file
Inside the `app` folder (created in Step 2), open `main.py` in your editor or Notepad.

Paste the following code:

```
from fastapi import FastAPI

app = FastAPI(title="DevOps Notes App")

@app.get("/healthz")
def healthz():
    # Health check endpoint (used by ops/monitoring)
    return {"status": "ok"}

@app.get("/hello")
def hello():
    # Simple demo endpoint
    return {"message": "Hello, world!"}
```
#### Run the app on port 8000

With the venv active, start Uvicorn:

```
uvicorn app.main:app --reload --port 8000
```
#### Verify it works

Open your browser:

> http://127.0.0.1:8000/healthz
 → {"status": "ok"}

> http://127.0.0.1:8000/hello
 → {"message": "Hello, world!"}

> http://127.0.0.1:8000/docs
 → interactive Swagger documentation.

---

## Step 6 — Containerise with Docker (run the same app anywhere)

This step packages the app **and** its dependencies into a lightweight image so it runs identically on any machine with Docker.

- Prereqs: Install Docker Desktop, open it, and ensure it says **Running**.  
- Verify in a terminal:

`docker --version`

---

#### Create a `.dockerignore` (keeps images small & clean)

Create a file named `.dockerignore` in the project root and paste:

```
pycache/
*.py[cod]
.venv/
venv/
ENV/
env/
.git/
.gitignore
.pytest_cache/
.DS_Store
Thumbs.db
*.log
build/
dist/

```
What this does:
Tells Docker not to copy these items into the image (prevents local junk getting added in).

---

#### Create a `Dockerfile` (the build recipe)

Create a file named `Dockerfile` in the project root:

dockerfile (Copy/Paste this into the file)
```
1) Small official Python base image
FROM python:3.11-slim

2) Non-root user (security best practice)
RUN useradd -m appuser

3) Work inside /app
WORKDIR /app

4) Copy dependency list first (better caching)
COPY requirements.txt .

5) Install Python dependencies inside the image
RUN pip install --no-cache-dir -r requirements.txt

6) Copy app source code
COPY app ./app

7) Run as non-root
USER appuser

8) Document the port the app listens on
EXPOSE 8000

9) Start the server when the container runs
0.0.0.0 makes it reachable from outside the container
CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8000"]
```
### What’s happening so far:

- FROM picks a tiny Linux + Python base.

- COPY requirements.txt + RUN pip install installs deps inside the image.

- COPY app brings in your FastAPI code.

- CMD [...] runs the app with Uvicorn on port 8000.

#### Build the Docker image

From the project root (where the Dockerfile lives):
```
docker build -t devops-notes-app:latest .
```
#### Run the container

```
docker run --rm -p 8000:8000 devops-notes-app:latest
```
### Open in your browser:

> http://127.0.0.1:8000/healthz

> http://127.0.0.1:8000/hello

> http://127.0.0.1:8000/docs

Stop the container with Ctrl + C in the terminal.

---
## REMEMBER
### Rebuild when you change code

Images are snapshots. If you edit Python files, rebuild and run again:
```
docker build -t devops-notes-app:latest .
docker run --rm -p 8000:8000 devops-notes-app:latest
```



