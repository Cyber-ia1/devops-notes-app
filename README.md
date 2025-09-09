# devops-notes-app
A small FastAPI web service to practise DevOps fundamentals (code → container → CI/CD → docs).

---

## Prerequisites
- Python 3.11 or newer
- Git
- (Optional) VS Code



Setup (Windows PowerShell)

## 1. Navigate to your project folder in powershell
```
cd C:\Users\<YourUsername>\Projects
mkdir devops-notes-app
cd devops-notes-app
```


## Step 2 — Create project structure (folders & files)

Set up a clean scaffold for the project so code, tests, and documentation are organized from the start.

create subfolders
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
