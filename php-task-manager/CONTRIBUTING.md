# Contributing to Daylist

Daylist is a small PHP task manager with a server-rendered interface and local
JSON storage. Read [README.md](README.md) for setup and project structure.

## Local workflow

1. Install PHP 8.0 or newer. Python 3.8+ is needed only for the chaos tool.
2. Work from the `php-task-manager` directory.
3. Check PHP syntax before starting the application:

   ```sh
   php -l app.php
   php -l public/index.php
   php -l public/health.php
   php -l public/about.php
   ```

4. Start the development server:

   ```sh
   php -S localhost:8000 -t public
   ```

5. Follow the [manual testing checklist](docs/manual-testing.md) for affected
   behavior. Record failures as well as successful checks.

## Change guidelines

- Keep changes focused on the requested behavior.
- Escape user-provided text when rendering HTML.
- Validate submitted values and persisted records before using them.
- Preserve CSRF checks for actions that change tasks.
- Keep storage updates protected by the existing file lock.
- Keep task data outside the public document root.
- Avoid committing personal task data or chaos-test backups.

## Review notes

Describe the problem, the resulting behavior, and the checks performed.
Mention any checks you could not run and any existing errors that blocked
verification. A PHP syntax check does not prove that required files exist or
that functions called at runtime are defined.
