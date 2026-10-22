# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Teacher sign-in for registering and unregistering students
- Public read-only activity and participant lists

## Getting Started

1. Install the dependencies:

   ```
   pip install fastapi uvicorn
   ```

2. Run the application:

   ```
   cd src
   cp teachers.example.json teachers.json
   python app.py
   ```

3. Add teacher credentials to `src/teachers.json`. Store a PBKDF2-SHA256 password hash and a unique random salt for each teacher, not a plaintext password. Generate a record with:

   ```
   python -c 'import getpass, hashlib, json, secrets; password=getpass.getpass("Password: "); salt=secrets.token_bytes(16); print(json.dumps({"teachers":[{"username":input("Username: "),"salt":salt.hex(),"password_hash":hashlib.pbkdf2_hmac("sha256",password.encode(),salt,200000).hex()}]}, indent=2))'
   ```

   Paste the generated JSON into `teachers.json`. The local credentials file is ignored by Git. Set `COOKIE_SECURE=true` when serving the app over HTTPS.

4. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                          | Description                                                         |
| ------ | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                     | Get all activities with their details and current participant count |
| POST   | `/auth/login`                                                      | Sign in as a teacher and receive a session cookie                   |
| POST   | `/auth/logout`                                                     | End the current teacher session                                     |
| GET    | `/auth/session`                                                    | Check the current teacher session                                   |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu` | Register a student (teacher session required)                       |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu` | Unregister a student (teacher session required)                  |

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
