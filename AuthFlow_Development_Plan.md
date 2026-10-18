# AuthFlow — Secure Authentication System Development Plan

## Project Overview

**Project name:** AuthFlow  
**Objective:** Build a complete authentication system with user registration, login, email OTP verification, and password recovery.  
**Estimated development time:** 3–5 days for a beginner, depending on experience.  
**Target cost:** ₹0 for local development and testing.

## 1. Features

| Module | Features | Priority |
|---|---|---|
| Login | Email, password, login button | Essential |
| Registration | Name, email, password, confirm password | Essential |
| Email verification | Send and verify OTP | Essential |
| Forgot password | Request OTP through email | Essential |
| Reset password | Verify OTP and create a new password | Essential |
| User dashboard | Welcome message, profile, logout | Essential |

### Possible future features

- Google Sign-In
- User profile management
- Remember me
- Login attempt protection
- Admin dashboard
- Email notifications for password changes

Build the essential features first to keep the project manageable.

## 2. Technology Stack

| Layer | Technology | Purpose |
|---|---|---|
| Frontend | HTML5 | Page structure |
| Styling | CSS3 | Interface design |
| Interactivity | JavaScript | Form validation and OTP inputs |
| Backend | Python + FastAPI | Authentication logic |
| Database | SQLite | User records |
| Password hashing | Argon2 | Secure password storage |
| Email service | Gmail SMTP | Send OTP emails |
| OTP generation | Python `secrets` | Generate secure random codes |
| Development environment | VS Code | Write and run the application |
| Version control | Git | Track changes |

> **Note:** Building authentication yourself is useful for learning. For production applications, consider a mature authentication framework or provider to reduce security risks.

## 3. Application Architecture

```text
                  USER
                   |
                   v
          FRONTEND APPLICATION
           HTML + CSS + JS
                   |
                   v
             FASTAPI BACKEND
                   |
          +--------+---------+
          |        |         |
          v        v         v
      AUTH API  OTP SERVICE  EMAIL SERVICE
          |        |         |
          |        |         v
          |        |      GMAIL SMTP
          |        |         |
          |        |         v
          |        |     USER'S EMAIL
          |        |
          +--------+
               |
               v
           SQLITE DB
```

### Component responsibilities

- **Frontend:** Displays forms and collects user input.
- **FastAPI:** Validates requests and implements authentication workflows.
- **SQLite:** Stores user accounts and relevant authentication records.
- **OTP service:** Generates and verifies time-limited codes.
- **Email service:** Sends OTPs to users.
- **Backend security:** Makes all final authentication decisions; the frontend must not be trusted to verify OTPs or passwords.

## 4. Development Roadmap

### Phase 1 — Design the User Interface (Day 1)

Build the pages before connecting them to a database.

**Login page**
- Email input
- Password input with show/hide option
- Login button
- Forgot Password link
- Create Account link

**Registration page**
- Full name
- Email
- Password
- Confirm password
- Create Account button

**Email verification page**
- Six-digit OTP input
- Verify button
- Resend OTP option

**Forgot Password page**
- Email input
- Send OTP button

**Reset Password page**
- New password
- Confirm new password
- Reset Password button

**Dashboard**
- Welcome message
- User name and email
- Profile section
- Logout button

**Design goal:** A clean, modern, responsive interface for desktop and mobile.

**Deliverable:** All pages working visually, before backend integration.

### Phase 2 — Build the Backend (Day 2)

Create the FastAPI application and connect it to SQLite.

The backend will handle:
- User registration
- Email validation
- Duplicate account prevention
- Password hashing
- Login verification
- User session management
- Logout

Suggested `users` table:

| Field | Purpose |
|---|---|
| `id` | Unique user identifier |
| `name` | User's name |
| `email` | Unique email address |
| `password_hash` | Secure password hash |
| `is_verified` | Whether email verification is complete |
| `created_at` | Account creation timestamp |

Never store users' actual passwords in the database.

**Deliverable:** Registration and login working with real database records.

### Phase 3 — Integrate Gmail OTP (Day 3)

#### A. Email verification during registration

```text
User registers
     ↓
Backend validates details
     ↓
OTP generated
     ↓
OTP emailed to user
     ↓
User enters OTP
     ↓
Backend verifies OTP
     ↓
Account becomes verified
```

#### B. Forgot-password flow

```text
User clicks Forgot Password
     ↓
Enters registered email
     ↓
Backend generates OTP
     ↓
OTP sent through Gmail SMTP
     ↓
User enters OTP
     ↓
Backend verifies OTP
     ↓
User creates a new password
     ↓
Password hash updated
```

#### Security requirements

- OTP expires after five minutes.
- Limit incorrect OTP attempts.
- Add a cooldown before an OTP can be resent.
- Store OTPs securely, preferably as keyed hashes.
- Use a single-use, time-limited password-reset authorization after OTP verification.
- Avoid responses that reveal whether an email address is registered.
- Rate-limit requests to reduce abuse.

**Deliverable:** Real OTP emails and a working password-reset process.

### Phase 4 — Connect the Frontend and Backend (Day 4)

Suggested API endpoints:

| Action | Method and endpoint |
|---|---|
| Register | `POST /auth/register` |
| Verify email | `POST /auth/verify-email` |
| Login | `POST /auth/login` |
| Request password reset | `POST /auth/forgot-password` |
| Verify reset OTP | `POST /auth/verify-reset-otp` |
| Reset password | `POST /auth/reset-password` |
| Logout | `POST /auth/logout` |

These are proposed routes and can be adjusted to suit the implementation.

Connect each form to the appropriate API and display helpful success or error messages.

**Deliverable:** A fully integrated authentication application.

### Phase 5 — Testing and Deployment (Day 5)

Test the following scenarios:

| Test | Expected result |
|---|---|
| Register with valid details | Account created |
| Register with an existing email | Request rejected safely |
| Login with correct credentials | Login successful |
| Login with an incorrect password | Login rejected |
| Enter an incorrect OTP | Verification rejected |
| Enter an expired OTP | Verification rejected |
| Reset password successfully | New password works |
| Reuse an OTP | Rejected |
| Access dashboard without authentication | Access denied |
| Logout and revisit dashboard | Authentication required |

Before public deployment, configure environment variables, HTTPS, secure session cookies, and persistent database storage.

Free hosting tiers may have usage limits or inactivity-related sleep behavior. Check the current terms of any provider before deployment.

## 5. Suggested Project Structure

```text
AuthFlow/
│
├── frontend/
│   ├── login.html
│   ├── register.html
│   ├── verify_email.html
│   ├── forgot_password.html
│   ├── reset_password.html
│   ├── dashboard.html
│   │
│   ├── css/
│   │   └── style.css
│   │
│   └── js/
│       ├── auth.js
│       └── otp.js
│
├── backend/
│   ├── main.py
│   ├── database.py
│   ├── models.py
│   ├── schemas.py
│   │
│   ├── routers/
│   │   └── auth.py
│   │
│   └── services/
│       ├── email_service.py
│       ├── otp_service.py
│       └── security.py
│
├── .env
├── .gitignore
├── requirements.txt
└── README.md
```

This structure separates the frontend, API routes, database, email delivery, OTP logic, and security functions.

## 6. Budget

| Item | Estimated cost for local development |
|---|---:|
| HTML, CSS, JavaScript | ₹0 |
| Python + FastAPI | ₹0 |
| SQLite | ₹0 |
| VS Code | ₹0 |
| Git | ₹0 |
| Gmail SMTP | ₹0 within permitted limits |
| Local testing | ₹0 |
| **Total** | **₹0** |

Gmail SMTP is available without a separate subscription, subject to Google's account policies, sending limits, and security requirements. A Gmail App Password may be required when using SMTP.

Google's setup instructions: https://support.google.com/accounts/answer/185833

Public hosting, a custom domain, or higher-volume email delivery may eventually cost money. You do not need to purchase these to complete the initial local project.

## 7. Recommended Implementation Order

1. Install Python and VS Code, if needed.
2. Create the project folder.
3. Build the login and registration pages.
4. Set up FastAPI.
5. Connect SQLite.
6. Implement secure registration and login.
7. Configure Gmail SMTP.
8. Implement OTP verification and password recovery.
9. Test the complete application.
10. Deploy it if public access is needed.

## 8. UI Design Direction

Choose a design direction before polishing the interface:

- **A. Modern SaaS:** Minimal white background, elegant typography, clean cards.
- **B. Dark developer theme:** Dark background, subtle gradients, technical aesthetic.
- **C. Premium AI startup:** Modern gradients, polished animations, split-screen layout.
- **D. Simple and professional:** Clean form, minimal styling, lightweight design.

**Suggested choice:** Premium AI startup, for a polished portfolio-ready appearance.

---

## Definition of Done

The initial version is complete when:
- Users can register and verify their email.
- Users can log in and log out.
- Passwords are securely hashed.
- Users can request an OTP to reset a forgotten password.
- OTPs expire and cannot be reused.
- Protected pages require authentication.
- Secrets are kept out of source control.
- Core authentication scenarios pass the test checklist.
