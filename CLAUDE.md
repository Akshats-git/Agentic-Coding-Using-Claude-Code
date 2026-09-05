# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository layout

This repo holds two unrelated halves:

- `Expense-Tracker/` — **Spendly**, a Flask expense-tracking web app. This is the only code in the repo.
- `Notes/` — written notes for the CampusX "Agentic Coding using Claude Code" playlist (15 videos). Prose only; treat as documentation and do not edit unless asked.

Spendly is the project built incrementally across that playlist, so it is a **deliberately partial starter app**. Several routes are stubs that return a placeholder string (see below) — they are unimplemented on purpose, not bugs to fix opportunistically.

## Commands

All commands run from `Expense-Tracker/`:

```bash
python3 -m venv venv && source venv/bin/activate   # first time only
pip install -r requirements.txt
python3 app.py            # dev server on http://127.0.0.1:5001 (debug=True)
```

Testing uses pytest + pytest-flask (installed, but no test suite exists yet):

```bash
pytest                    # whole suite
pytest tests/test_auth.py::test_login_success   # single test
pytest -k "login" -v      # by name
```

Python 3.12. There is no build step, bundler, linter, or formatter configured — do not introduce one without being asked.

## Architecture

**Single-module Flask app.** `Expense-Tracker/app.py` holds the app object and every route; there are no blueprints. Routes are grouped under two banner comments: implemented routes at the top, then a "Placeholder routes — students will implement these" block (`/logout`, `/profile`, `/expenses/add`, `/expenses/<id>/edit`, `/expenses/<id>/delete`). Each placeholder names the course step that fills it in. Keep new routes in the matching group and move a route out of the placeholder block when implementing it.

**Database layer is an unwritten stub.** `database/db.py` is a comment block only. The intended contract, stated in that file, is three functions: `get_db()` (SQLite connection with `row_factory` set and foreign keys enabled), `init_db()` (`CREATE TABLE IF NOT EXISTS`), and `seed_db()` (sample dev data). The database file is `expense_tracker.db` at the app root and is gitignored — never commit it. No ORM: raw `sqlite3` with parameterised queries.

**Templates.** Jinja2, all in `templates/`, all extending `base.html`, which owns the navbar, footer, Google Fonts links, the single stylesheet link, and the blocks `title`, `head`, `content`, `scripts`. Always link between pages with `url_for('<endpoint>')` and reference assets with `url_for('static', filename=...)`, never hardcoded paths.

**Styling is one hand-written stylesheet.** `static/css/style.css` (~680 lines) is the entire design system: a `:root` block of CSS custom properties (`--ink*`, `--paper*`, `--accent`, `--border*`, `--font-display`/`--font-body`, `--radius-*`, `--max-width`) followed by sections separated by full-width banner comments (Reset, Navbar, Hero, Buttons, Auth pages, Legal / content pages, Footer, Modal, Responsive). Add new styles inside the matching section or add a new banner section before Responsive, and use the existing variables rather than literal colours. The look is a warm paper/ink editorial theme — serif display headings, sans body. No CSS framework, no dark mode.

**JavaScript is minimal and page-local.** `static/js/main.js` is intentionally empty. Page-specific behaviour lives in a `{% block scripts %}` at the bottom of that page's template, wrapped in an IIFE, using `var` and plain DOM APIs (see the "See how it works" modal in `landing.html`). No jQuery, no framework, no bundler.

## Conventions

- Currency is **INR**, displayed with `₹`. Never use `$` or USD in copy, mock data, or seeds.
- The product name in user-facing copy is **Spendly**; the brand glyph is `◈`.
- Form pages POST to their own path (`/login`, `/register`) and render an `error` variable through the `auth-error` div — follow that pattern for validation feedback rather than adding flash messaging.
- Passwords must be hashed with `werkzeug.security` (werkzeug is already a pinned dependency); registration copy promises a minimum of 8 characters.
- Pin new dependencies exactly in `requirements.txt`, matching the existing `package==x.y.z` style.
- Commit messages use a lowercase `area: imperative summary` form (`landing: add privacy policy page and route`).

## Workflow used in this repo

The playlist's spec-driven flow is the working style here: for a new feature, write the spec first, then a technical plan, then code, then validate against the spec's acceptance criteria — one feature per branch, merged to `main` via PR. `.claude/plans/` is gitignored, so plan files stay local.
