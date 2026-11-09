# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Sign up for activities

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Run the application:

   ```
   python app.py
   ```

3. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                          | Description                                                         |
| ------ | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                     | Get all activities with their details and current participant count |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu` | Sign up for an activity (teacher login required)                    |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu` | Remove a student from an activity (teacher login required)         |
| POST   | `/auth/login`                                                      | Sign in as a teacher                                                |
| POST   | `/auth/logout`                                                     | Sign out                                                            |
| GET    | `/auth/session`                                                    | Check the current login state                                       |

## Teacher Access

Copy `src/teachers.example.json` to `src/teachers.json` to configure teacher accounts. The local credentials file is ignored by Git. Add a record containing a username, a random salt in hex, and a PBKDF2-SHA256 password hash. Generate a record interactively so the password is not included in shell history:

```bash
python - <<'PY'
import getpass
import hashlib
import json
import secrets

username = input("Username: ")
password = getpass.getpass("Password: ")
salt = secrets.token_bytes(16)
password_hash = hashlib.pbkdf2_hmac("sha256", password.encode(), salt, 600000)
print(json.dumps({
   "username": username,
   "salt": salt.hex(),
   "password_hash": password_hash.hex(),
}, indent=2))
PY
```

Paste the printed object into the `teachers` array in `src/teachers.json`. The app uses an HttpOnly, SameSite=Strict session cookie that expires after eight hours. Set `COOKIE_SECURE=true` when serving over HTTPS.

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.
