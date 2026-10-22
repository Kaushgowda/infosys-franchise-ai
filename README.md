# infosys-franchise-ai
```markdown name=README.md
# Infosys Portal

A single-file Streamlit authentication application built for the Infosys Springboard Internship 7.0 — Batch 4 | Milestone 1 project.

This project combines signup, login, password reset, OTP verification, JWT-based session handling, admin access, and a simple user dashboard into one application.

## Overview

This app includes:

- User signup with validation
- Login for both user and admin
- Email validation
- Password strength validation
- Confirm-password matching
- Password hashing with bcrypt
- Forgot password using:
  - Security question answer
  - Email OTP
- OTP expiration after 5 minutes
- JWT-based login sessions
- Basic lockout mechanism after repeated failed login attempts
- Admin dashboard showing registered users
- User dashboard after successful login

## Tech Stack

- Python
- Streamlit
- SQLite
- bcrypt
- PyJWT
- SMTP (Gmail)
- Pyngrok (for public URL in Google Colab)

## Project Files

- `Infosys_Milestone1_Single_App.ipynb` — main notebook that creates and runs the app
- `app.py` — generated single-file Streamlit application

## Features in Detail

### User Authentication
- New users can create an account with:
  - full name / username
  - email
  - password
  - security question and answer
- Passwords are hashed before storage.
- Only the email is used for login.

### Password Validation
The app enforces a strong password policy:
- minimum 8 characters
- at least one uppercase letter
- at least one lowercase letter
- at least one numeric digit
- at least one special character

### Security Features
- Passwords are stored as bcrypt hashes
- Security answers are also hashed
- JWT tokens are used for authenticated sessions
- Tokens expire after 2 hours
- OTPs expire after 5 minutes
- Failed login attempts are tracked for temporary lockout

### Admin Access
Admin login is separate from the normal user database. Credentials are expected to be provided as environment secrets.

### Admin Dashboard
Admins can view:
- total number of registered users
- usernames
- emails
- registration timestamps

### User Dashboard
Users can:
- log in
- access their dashboard
- log out

---

## Setup Instructions

### 1. Install dependencies
Run this in the notebook:

```python
!pip install -q streamlit pyngrok pyjwt bcrypt
```

### 2. Add required secrets
In Google Colab, open the Secrets panel and add:

- `JWT_SECRET`
- `NGROK_AUTHTOKEN`
- `EMAIL_ADDRESS`
- `EMAIL_PASSWORD`
- `ADMIN_USERNAME`
- `ADMIN_PASSWORD`

Important:
- `EMAIL_PASSWORD` must be a Gmail App Password, not your normal Gmail password.
- Keep all secrets private.

### 3. Run the app generation cell
The notebook contains a cell that writes the main application file:

```python
%%writefile app.py
```

### 4. Launch the app
Run the final launcher cell in the notebook. It will:
- set the secret environment variables
- start the Streamlit app
- expose it via ngrok
- print the public URL

---

## App Flow

1. User opens the app.
2. User chooses Login or Create Account.
3. New users register with email, password, and security answer.
4. Existing users log in.
5. If password is forgotten, user selects:
   - security question method
   - email OTP method
6. After successful reset, the user is redirected to login.
7. Admin can log in separately and view all registered users.

---

## Security Notes

- Never share the `.db` file or secret values publicly.
- Use strong JWT secrets.
- Use Gmail App Passwords for email sending.
- For production use, this app should be hardened further with:
  - HTTPS enforcement
  - stronger database protection
  - rate limiting
  - environment-based config separation
  - role-based access control improvements

---

## Running Locally

If you want to run the app manually on a local machine:

```bash
pip install streamlit pyjwt bcrypt
export JWT_SECRET="your_secret_key"
export EMAIL_ADDRESS="your_email@gmail.com"
export EMAIL_PASSWORD="your_gmail_app_password"
export ADMIN_USERNAME="admin"
export ADMIN_PASSWORD="secure_password"
streamlit run app.py
```

---

## Notes

This project is intended as a student learning and internship project. It is suitable for testing and demo use, but not as a full production-grade authentication system without additional security and deployment hardening.

## License

This project is provided for educational use. No explicit license was included in the project source.
```

If you want, I can also generate:
- a more polished GitHub-style README
- a shorter project README
- a version with screenshots and architecture diagram
- a README in Hindi/English bilingual format

<img width="1418" height="837" alt="image" src="https://github.com/user-attachments/assets/7bdf7c02-b9a7-4e2f-9d52-f5f4078bdea6" />

<img width="1136" height="759" alt="Screenshot 2026-10-22 191755" src="https://github.com/user-attachments/assets/fc179fb9-1d5b-46cd-92c8-071e4ce752b8" />

<img width="1262" height="866" alt="image" src="https://github.com/user-attachments/assets/9c243e05-4fba-49a9-9877-df495349f94a" />
<img width="1492" height="872" alt="image" src="https://github.com/user-attachments/assets/fc81e47a-3dcf-4e1a-b053-4eb091a7c88e" />




