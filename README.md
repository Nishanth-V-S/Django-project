# Feedback Management System

A responsive Django application for a college or institution. Students can create accounts, submit private feedback about teaching, courses and campus services, and review their own submissions. Staff accounts have a separate dashboard for feedback statistics, search, filtering, detail review and deletion.

## Features

- Django registration, password hashing, login and POST logout
- Student dashboard with submission count, average rating and recent entries
- Feedback form with eight categories and validated one-to-five ratings
- Category-aware details: faculty/course, campus facility, laboratory, library, canteen, transport, or other subject fields are only saved when relevant
- A 500-character comment limit, accessible star selector, category filters, and a post-submit success page
- Owner-scoped feedback list and detail pages
- Staff-only analytics and feedback management with search and filters
- Django admin configuration for staff and feedback records
- SQLite database and a demo-data management command

## Technologies

Python 3, Django 5.2, SQLite, Django Templates and Forms, HTML, CSS, Bootstrap 5, and small vanilla JavaScript confirmation handling.

## Installation

From the project directory:

```bash
python -m venv venv
```

Windows PowerShell:

```powershell
venv\Scripts\Activate.ps1
```

Install dependencies and initialize the database:

```bash
pip install -r requirements.txt
python manage.py makemigrations
python manage.py migrate
python manage.py createsuperuser
python manage.py runserver
```

Open <http://127.0.0.1:8000/>. Use the superuser username and password to sign in to the website's staff dashboard. The Django administration site is at <http://127.0.0.1:8000/django-admin/>.

## Demo data

After migrations, run `python manage.py seed_feedback`. It creates a demo student (`demo_student`, password `DemoPass123!`) and three example entries with different ratings/categories. The command is optional and only creates data when those entries do not already exist. Do not use its sample password outside a local demo.

## How it works

The registration form extends Django's `UserCreationForm`, so passwords are validated and hashed. Feedback rows are associated with the signed-in user. Student views filter by that owner, while detail views enforce ownership; staff access is limited to accounts with `is_staff`. The admin dashboard aggregates counts and averages through Django ORM queries. All data-changing actions use CSRF-protected POST forms.

The category-specific details were added in migration `0002_category_specific_details`, which extends the existing feedback table and keeps previous feedback rows. The submission form enforces required fields for the chosen category on the server and clears unrelated category details before saving.

## Project structure

```text
feedback_management/          Django project configuration and entry points
feedback/                     Application code
  migrations/                 Database schema history
  management/commands/        Optional sample-data command
  templates/feedback/         Shared and page-specific templates
  static/feedback/             CSS and JavaScript
  models.py                   Feedback database model
  forms.py                    Registration and feedback forms
  views.py                    Authentication, student and staff workflows
  urls.py                     Named application routes
  admin.py                    Django admin customization
manage.py                     Django command-line entry point
requirements.txt              Minimal dependency list
```

## User and administrator flows

Students register, log in, submit feedback, review their personal list and open their own details. A staff user (created with `createsuperuser`, or granted `is_staff` in Django admin) logs in and is directed to the administrator dashboard. The dashboard summarizes users, total and daily feedback, average rating, categories and star distribution; the management page supports search/filter/sort, detail review and POST deletion.

## Security notes

The included development settings enable `DEBUG` and use a development-only secret key. Before deployment, set a private `SECRET_KEY`, disable debug, configure allowed hosts, HTTPS/cookie settings, and use a production-grade static-file setup. Staff page access uses `is_staff`; superusers are staff by default.

## Manual verification checklist

- Register with matching passwords; try duplicate username/email and mismatched passwords.
- Log in as a student; open dashboard; submit feedback with each category/rating; inspect My Feedback and a detail page; log out.
- Verify a student cannot access `/admin-dashboard/` and cannot view another student's feedback detail.
- Log in with a staff account; inspect metrics, category/rating distributions, search/filter/sort results, details and deletion.
- Check logged-out requests to dashboard and submission redirect to login; check an unknown feedback ID returns 404.

## Django file guide

- `settings.py`: installed apps, middleware, SQLite, templates, timezone and authentication redirects.
- `urls.py`: project routes and Django's built-in administration route.
- `models.py`: feedback fields, choices, validators and ordering.
- `forms.py`: registration/email validation and styled feedback ModelForm.
- `views.py`: page rendering, login flow, ownership checks, staff analytics and management.
- `admin.py`: searchable and filterable Django admin list.
- `templates/feedback/`: shared navigation and page templates.
- `static/feedback/`: responsive styling and delete confirmation behavior.
- `migrations/0001_initial.py`: initial Feedback schema migration.
- `management/commands/seed_feedback.py`: optional repeat-safe local sample data.
