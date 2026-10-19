# Manual testing checklist

These checks describe expected behavior, not a record of passing tests.
Use a local development instance. Stop the server and back up existing
`data/tasks.json` before testing changes that affect storage.

## Startup

- [ ] Run the PHP syntax checks in [CONTRIBUTING.md](../CONTRIBUTING.md).
- [ ] Start the server with `php -S localhost:8000 -t public`.
- [ ] Open `http://localhost:8000/` and confirm the task interface renders.
- [ ] Open `/health.php` and confirm the JSON contains `status: "ok"` and
      `app: "Daylist"`. This endpoint does not verify the task page or storage.
- [ ] Check the server terminal for errors. If startup fails, record the
      failure before attempting task operations.

## Task lifecycle

- [ ] Add a task with a recognizable title; verify the total increases by one.
- [ ] Refresh and confirm the task persists.
- [ ] Edit the title and confirm the new text remains after refreshing.
- [ ] Complete the task and confirm it appears in Completed and disappears
      from In progress.
- [ ] Reopen the task and confirm the filters and counts update.
- [ ] Open Delete without confirming; verify the task remains.
- [ ] Confirm deletion and verify the task disappears after refreshing.

## Input and layout

- [ ] Try submitting a whitespace-only title; it should not create a task.
- [ ] Add `<b>hello</b>` as a title; it should appear as literal text.
- [ ] Use the interface with the keyboard and confirm controls are reachable.
- [ ] Check a narrow browser window for readable text and usable controls.
- [ ] Check an empty filter for a clear empty-state message.

## Optional malformed-data check

Follow the [README chaos-test instructions](../README.md#python-chaos-test).
Stop the server before injection and restoration. Verify valid tasks remain
visible and malformed records do not cause rendering errors. Restoration
discards task edits made during the test, so use disposable test data.

## Record the result

For a failure, note the action, expected behavior, actual behavior, and relevant
terminal error. Include the PHP version and browser used. Do not include
personal task contents or session tokens in a report.
