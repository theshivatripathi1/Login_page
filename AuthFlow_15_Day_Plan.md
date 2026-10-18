# AuthFlow — 15-Day Implementation & Learning Roadmap

**Project:** AuthFlow (Full-Stack Secure Authentication System)  
**Pacing:** ~1.5 to 2.5 hours per day (Ideal for thorough learning, clean coding, and production-grade security)  
**Budget:** ₹0 (Local development using SQLite, FastAPI, Vanilla JS/CSS, and Gmail SMTP)

---

## Roadmap Overview

```text
  WEEK 1 (Days 01–05) ──► Frontend & UI/UX Foundation (Premium AI Theme)
  WEEK 2 (Days 06–10) ──► Backend Architecture, DB & Security Engine
  WEEK 3 (Days 11–15) ──► Integration, Hardening, Testing & Documentation
```

---

## Week 1: Environment Setup & UI/UX Design

### Day 1: Project Setup & Design Architecture
* **Goal:** Initialize repository, development environment, and project directory structure.
* **Tasks:**
  - [x] Initialize Git repository with proper `.gitignore` (Python, OS files, `.env`).
  - [x] Set up Python virtual environment (`venv`) and initial `requirements.txt`.
  - [x] Create the project directory structure (`frontend/`, `backend/`, `routers/`, `services/`).
  - [x] Establish design tokens in `frontend/css/style.css` (color palette, typography, glassmorphism variables, dark mode base).
* **Deliverable:** Working folder structure and base CSS design system. (Completed)

---

### Day 2: Authentication Shell & Navigation Layout
* **Goal:** Create shared components and base visual shell.
* **Tasks:**
  - [ ] Build shared navigation and header (Logo, brand tag, dark/light ambient background).
  - [ ] Build notification toast container (for success/error/warning messages).
  - [ ] Implement responsive viewport scaling and mobile-first container grid.
  - [ ] Add SVG icons for inputs (email, lock, eye, checkmark, shield).
* **Deliverable:** Reusable layout shell and toast notification system.

---

### Day 3: Registration & Email Verification Pages
* **Goal:** Build the user onboarding interface.
* **Tasks:**
  - [ ] Create `register.html` with fields: Full Name, Email, Password, Confirm Password.
  - [ ] Add real-time password strength indicator (length, numbers, uppercase, special characters).
  - [ ] Create `verify_email.html` with 6-digit split OTP input boxes.
  - [ ] Add JavaScript for OTP input behavior: auto-focus next input, backspace navigation, paste support.
  - [ ] Implement resend OTP countdown timer (e.g., 60-second cooldown).
* **Deliverable:** Fully functional, interactive registration & OTP verification screens.

---

### Day 4: Login & Password Recovery Pages
* **Goal:** Build the login and password reset client views.
* **Tasks:**
  - [ ] Create `login.html` with Email, Password, Show/Hide password toggle, and "Remember Me" checkbox.
  - [ ] Create `forgot_password.html` (Email input with clear instructions).
  - [ ] Create `reset_password.html` (New Password, Confirm Password, OTP verification token handle).
  - [ ] Add client-side form validation before submission (regex checks, empty field checks).
* **Deliverable:** Polished, responsive login and recovery interfaces.

---

### Day 5: User Dashboard & UI Polish Review
* **Goal:** Build the authenticated landing experience and conduct UI audit.
* **Tasks:**
  - [ ] Create `dashboard.html` with user greeting, profile card, account status badge (`Verified`), and logout button.
  - [ ] Add interactive states (hover effects, loading button spinners during API requests).
  - [ ] Mobile & responsiveness testing across screen sizes (DevTools).
  - [ ] Accessibility review: proper `<label>`, `aria-*` tags, keyboard navigability.
* **Deliverable:** Complete, standalone 6-page frontend ready for API wiring.

---

## Week 2: Backend Architecture, Database & Security

### Day 6: FastAPI Foundation & SQLite Models
* **Goal:** Scaffold the FastAPI application and configure the database layer.
* **Tasks:**
  - [ ] Install FastAPI, Uvicorn, SQLAlchemy, Pydantic v2.
  - [ ] Configure `backend/database.py` with SQLite connection and session maker.
  - [ ] Create `backend/models.py` with `User` table schema (`id`, `name`, `email`, `password_hash`, `is_verified`, `created_at`).
  - [ ] Set up database migration/init script to generate the SQLite database.
  - [ ] Implement basic health check endpoint (`GET /health`).
* **Deliverable:** FastAPI running locally with connected SQLite database.

---

### Day 7: Password Security & Registration Endpoint
* **Goal:** Implement secure user registration with Argon2 hashing.
* **Tasks:**
  - [ ] Install and configure `argon2-cffi` for password hashing and verification.
  - [ ] Create Pydantic validation schemas in `backend/schemas.py` (`UserCreate`, `UserResponse`).
  - [ ] Implement email format validation and lowercase normalization.
  - [ ] Build `POST /auth/register` route in `backend/routers/auth.py`.
  - [ ] Prevent duplicate registrations while returning safe, consistent responses.
* **Deliverable:** Working registration API that securely hashes passwords into SQLite.

---

### Day 8: Authentication Engine & JWT / Session Cookies
* **Goal:** Build user login and token generation.
* **Tasks:**
  - [ ] Configure `python-jose` for JWT generation (or secure HTTP-only session cookies).
  - [ ] Build `POST /auth/login` endpoint validating email and Argon2 password hash.
  - [ ] Enforce email verification check (prevent unverified users from accessing protected resources).
  - [ ] Implement `get_current_user` FastAPI dependency for route protection.
  - [ ] Build protected `GET /auth/me` endpoint to fetch profile data.
* **Deliverable:** Secure login API issuing tokens and protecting private endpoints.

---

### Day 9: Email Delivery Engine (Gmail SMTP)
* **Goal:** Set up transactional email delivery.
* **Tasks:**
  - [ ] Configure Google Account App Password for SMTP access.
  - [ ] Store credentials securely in `.env` (`SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASS`).
  - [ ] Create `backend/services/email_service.py` using Python's `aiosmtplib` or standard `smtplib`.
  - [ ] Design clean, responsive HTML email templates for OTP delivery.
  - [ ] Test sending a test email to verify SMTP handshake and deliverability.
* **Deliverable:** Automated email service capable of dispatching formatted OTP emails.

---

### Day 10: Cryptographic OTP Engine
* **Goal:** Build a secure, time-limited OTP system.
* **Tasks:**
  - [ ] Create `OTPRecord` table/cache schema (`email`, `otp_hash`, `purpose`, `expires_at`, `attempts`).
  - [ ] Build `backend/services/otp_service.py` using Python `secrets` for 6-digit random code generation.
  - [ ] Implement security rules:
    - 5-minute time-to-live (TTL).
    - Maximum 5 failed attempts per OTP before invalidation.
    - 60-second resend cooldown timer.
    - Store OTPs as hashes, never in plain text.
  - [ ] Build `POST /auth/verify-email` and `POST /auth/resend-otp` endpoints.
* **Deliverable:** Full end-to-end email verification with real emails and one-time codes.

---

## Week 3: Integration, Hardening, Testing & Documentation

### Day 11: Password Recovery Pipeline
* **Goal:** Implement the complete forgot password and reset password workflow.
* **Tasks:**
  - [ ] Build `POST /auth/forgot-password`: Generates OTP and sends reset email.
  - [ ] Implement enumeration mitigation: always return generic success message even if email doesn't exist.
  - [ ] Build `POST /auth/verify-reset-otp`: Validates OTP and returns a single-use, 10-minute reset token.
  - [ ] Build `POST /auth/reset-password`: Accepts new password + reset token, updates Argon2 hash, and revokes token.
* **Deliverable:** Fully functional backend password recovery pipeline.

---

### Day 12: Frontend-Backend Integration (Registration & Login)
* **Goal:** Wire client onboarding and login forms to the FastAPI backend.
* **Tasks:**
  - [ ] Create `frontend/js/api.js` for unified `fetch()` requests and error parsing.
  - [ ] Connect `register.html` form $\rightarrow$ `POST /auth/register` $\rightarrow$ redirect to OTP screen.
  - [ ] Connect `verify_email.html` form $\rightarrow$ `POST /auth/verify-email` $\rightarrow$ redirect to login.
  - [ ] Connect `login.html` form $\rightarrow$ `POST /auth/login` $\rightarrow$ store auth token $\rightarrow$ redirect to dashboard.
  - [ ] Implement inline error messages and toast feedback for invalid inputs or credentials.
* **Deliverable:** Live registration and login flow working from the browser.

---

### Day 13: Frontend-Backend Integration (Password Reset & Dashboard)
* **Goal:** Wire password reset flow and protected dashboard.
* **Tasks:**
  - [ ] Connect `forgot_password.html` $\rightarrow$ `POST /auth/forgot-password`.
  - [ ] Connect `reset_password.html` $\rightarrow$ verify reset OTP and submit new password.
  - [ ] Implement route guards on `dashboard.html`: verify token via `GET /auth/me`, redirect to login if unauthorized.
  - [ ] Connect Logout button: clear auth tokens and redirect to login.
  - [ ] Display logged-in user's name, email, and creation date on the dashboard.
* **Deliverable:** Completely integrated full-stack authentication web application.

---

### Day 14: Security Hardening & Edge Cases
* **Goal:** Defend against common web vulnerabilities.
* **Tasks:**
  - [ ] Configure CORS middleware strictly in FastAPI.
  - [ ] Implement rate limiting using `slowapi` on sensitive routes (`/login`, `/forgot-password`, `/verify-email`).
  - [ ] Add security response headers (Content-Security-Policy, X-Frame-Options, X-Content-Type-Options).
  - [ ] Validate edge cases: empty strings, SQL injection attempts, expired tokens, tampered JWTs.
  - [ ] Ensure no sensitive data is leaked in console logs or error traces.
* **Deliverable:** Production-grade security layer guarding the authentication endpoints.

---

### Day 15: Comprehensive Testing, Documentation & Review
* **Goal:** Execute test checklist, write documentation, and finalize repository.
* **Tasks:**
  - [ ] Run through the complete 10-point test checklist:
    - [ ] Register with valid details
    - [ ] Register with duplicate email
    - [ ] Login with valid/invalid credentials
    - [ ] Unverified user login restriction
    - [ ] Valid, expired, and incorrect OTP entries
    - [ ] OTP brute-force attempt lockout
    - [ ] Password reset with valid/invalid tokens
    - [ ] Unauthorized access to `/dashboard.html`
    - [ ] Logout and back-button behavior
  - [ ] Write a comprehensive `README.md` with:
    - Architecture diagram
    - Setup instructions (Python venv, dependencies, Gmail App Password)
    - Environment variable guide (`.env.example`)
    - API documentation and route list
  - [ ] Final Git commit and codebase cleanup.
* **Deliverable:** Fully tested, polished, portfolio-ready AuthFlow project.

---

## Daily Schedule Summary

| Day | Focus Area | Key Output |
| :---: | :--- | :--- |
| **01** | Setup & Design System | Repo, virtual environment, base CSS design tokens |
| **02** | UI Shell & Shared Components | Layout header, toast notifications, responsive grid |
| **03** | Register & OTP Verification UI | Registration form, strength meter, 6-box OTP input |
| **04** | Login & Password Recovery UI | Login page, forgot password page, reset page |
| **05** | Dashboard UI & Frontend Review | Dashboard view, animations, mobile responsiveness |
| **06** | FastAPI Setup & SQLite Schema | API scaffolding, SQLAlchemy models, DB init |
| **07** | Password Hashing & Registration | Argon2id hashing, `POST /auth/register` |
| **08** | Login Engine & Auth Tokens | `POST /auth/login`, JWT/Session, `get_current_user` |
| **09** | Gmail SMTP Email Service | App password setup, HTML email templates, SMTP sender |
| **10** | OTP Generation & Verification | Secure OTP generator, 5-min TTL, `POST /auth/verify-email` |
| **11** | Password Recovery Pipeline | Forgot password & reset password API endpoints |
| **12** | Frontend Integration (Part 1) | Register, OTP verify, and Login wired with `fetch()` |
| **13** | Frontend Integration (Part 2) | Forgot/Reset password & Protected Dashboard wired |
| **14** | Security Hardening | Rate limiting (SlowAPI), CORS, security headers |
| **15** | Testing, Polish & Documentation | 10-point test checklist, `README.md`, final cleanup |
