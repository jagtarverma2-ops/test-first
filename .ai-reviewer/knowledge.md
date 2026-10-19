# jagtarverma2-ops/test-first reviewer notes

## Architecture

Daylist is a small server-rendered PHP task manager with no framework, JavaScript, database, or Composer dependencies (`php-task-manager/README.md`). The application stores tasks in `data/tasks.json`, with request handling, validation, sessions, CSRF checks, and locked file persistence intended to live in `app.php`; `public/index.php` renders the HTML interface. `public/about.php`, `public/health.php`, and `tools/chaos.py` provide ancillary pages and storage-resilience testing.

## Conventions

- Keep the web document root at `php-task-manager/public`; application code and `data` should remain outside it (`php-task-manager/README.md`).
- Use server-rendered forms and POST actions for mutations. Supported actions are explicitly allowlisted as `add`, `edit`, `toggle`, and `delete` in `php-task-manager/app.php`.
- Protect every mutation with the session CSRF token and redirect with HTTP 303 after success (`php-task-manager/app.php`).
- Treat persisted JSON as untrusted input. `taskStore()` filters records to non-empty string IDs and titles, titles up to 200 bytes, and boolean `done` values before rendering or mutation (`php-task-manager/app.php`).
- Serialize storage access with an exclusive `flock()` and write through rewind/truncate/flush while the lock is held (`php-task-manager/app.php`).
- Escape all user-controlled output with `htmlspecialchars(..., ENT_QUOTES | ENT_SUBSTITUTE, 'UTF-8')`; task titles, IDs, messages, errors, and form values follow this pattern in `public/index.php`.
- Use accessible, progressively enhanced HTML: labeled controls, `aria-label`/`aria-current`, `<details>` for edit/delete flows, and `role="alert"` or `role="status"` for notices (`public/index.php`).
- The chaos tool deliberately injects scalars, missing fields, and wrong types, then uses an exclusive backup and atomic `os.replace()` restoration (`tools/chaos.py`). Do not weaken PHP validation to accommodate malformed records.

## Intentional non-standard choices

- This is deliberately a local, single-user application without accounts; all visitors to one running instance share `data/tasks.json` (`php-task-manager/README.md`).
- JSON-file persistence and file locking replace a database; do not flag the absence of migrations, models, or database transactions.
- No client-side JavaScript is used; editing, confirmation, filtering, and status changes are implemented with native HTML and POST requests.
- `public/health.php` intentionally returns a minimal uncached JSON health response rather than using the application UI.

## Watch out for

- Verify bootstrap wiring: `public/index.php` requires `bootstrap.php`, but the supplied file list documents `app.php`; ensure the referenced file exists and exposes `$tasks`, `$completed`, `$filter`, `$visible`, `escape()`, and `formFields()`.
- Flag syntax or typo regressions in `php-task-manager/app.php`: the sample contains malformed declarations/calls such as `escape(stg`, `b2hex`, the truncated filter assignment, and an invalid `formFields` return-type declaration.
- Ensure POST callbacks validate the submitted task ID as a string and do not trust arbitrary `$_POST` types.
- Preserve byte-length validation and output escaping; changing either can permit oversized or unsafe titles.
- Keep `data` inaccessible from the web root and ensure storage writes remain locked and truncated/atomic enough to avoid stale trailing JSON.