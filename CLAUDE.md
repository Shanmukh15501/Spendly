# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**Spendly** — a Flask-based personal expense tracker with Indian Rupee (₹) focus. The project is in active early-stage development; the landing page and auth forms are complete, but backend logic and database are stubs waiting to be implemented.

GitHub: https://github.com/Shanmukh15501/Spendly

## Dev Commands

```bash
# Activate virtual environment (Windows)
venv\Scripts\activate

# Run the development server (port 5001, debug mode)
python app.py

# Run tests
pytest
```

No build step — static assets are served directly by Flask.

## Architecture

### Stack
- **Backend**: Flask 3.1.3, SQLite (`expense_tracker.db`)
- **Frontend**: Jinja2 templates, vanilla JS, single CSS file (`static/css/style.css`)
- **Testing**: pytest + pytest-flask

### Key Files
- `app.py` — all Flask routes; public routes first, then placeholder routes numbered by implementation step
- `database/db.py` — stub with `get_db()`, `init_db()`, `seed_db()` for students to implement
- `templates/base.html` — base layout (navbar + footer); all pages extend this
- `static/css/style.css` — unified 1100+ line stylesheet with CSS variables for the design system

### Routing Pattern
Routes are grouped:
1. **Public/static**: `/`, `/terms`, `/privacy`
2. **Auth**: `/register`, `/login`, `/logout`
3. **Expenses CRUD**: `/expenses/add` (POST), `/expenses/<id>/edit` (PUT), `/expenses/<id>/delete` (DELETE)
4. **User**: `/profile`

Placeholder routes are marked with step numbers in comments and return stub responses until implemented.

### Template Inheritance
All pages extend `base.html` using `{% block content %}` and an optional `{% block scripts %}` for page-specific JS. Error messages are passed to templates via the `error` Jinja2 variable.

### Design System (CSS Variables)
- **Primary**: `#1a472a` (accent green — buttons, brand)
- **Secondary**: `#c17f24` (warm gold — highlights)
- **Background**: `#f7f6f3` (paper)
- **Fonts**: DM Serif Display (headings), DM Sans (body)
- Responsive breakpoints at 900px and 600px (mobile-first)

## Implementation Status

| Area | Status |
|---|---|
| Landing page | Complete |
| Auth forms (HTML) | Complete |
| Auth backend (register/login POST) | Stub |
| Database schema | Stub |
| Expense CRUD | Stub |
| Dashboard/profile views | Stub |
| `static/js/main.js` | Stub |
