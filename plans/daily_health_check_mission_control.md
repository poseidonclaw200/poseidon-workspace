Plan: Implement a Daily Health Dashboard for MissionControl

Goal

    Provide a lightweight, repeatable daily snapshot of the app and project health so the team can get a single glance at key signals each morning (commits, tests, jobs, errors, task throughput, backups).

Scope

    Back-end: periodic job that collects metrics and writes a DailyHealth model record.
    Front-end: dashboard page (protected) that shows the latest entry and a 7/30-day history with trend mini-charts.
    Integrations: Git commit count, CI/test status, task counts by status/tag, Celery/job run stats, recent errors (Sentry or log parsing), DB size/backups, and optional uptime/response time.
    Non-goals (for MVP): full analytics engine, expensive historical queries, or exposing sensitive logs to public.

High-level timeline (MVP in ~3–7 days)

    Day 1: Spec + data model + periodic job skeleton
    Day 2: Collectors for each metric (git, tests, tasks, jobs, errors)
    Day 3: UI + templates + permissions
    Day 4: Tests, deploy to staging, schedule cron/Celery beat
    Day 5–7: Iterate, add alerts, tidy logging, release to production

Concrete design

    Data model (Django)

    Model: DailyHealth
        date (DateField, unique)
        commits_count (Integer)
        ci_status (CharField / JSONField) — e.g., {"status":"passing","last_run":"..."}
        open_tasks (Integer)
        tasks_by_status (JSONField) — {"todo":12,"in_progress":3,"done":20}
        failing_jobs (Integer)
        job_stats (JSONField) — runtimes, last_success, last_failure
        errors_count (Integer)
        top_errors (JSONField) — small list of recent top errors (message, count)
        db_size_bytes (BigInteger)
        last_backup (DateTime)
        notes (TextField) — freeform summary
        created_at / updated_at

    Index: date for fast lookups; keep retention policy (e.g., keep 1 year of daily rows).

    Metric collectors

    Implement a central collector function that assembles the dict for DailyHealth.
    Collector responsibilities:
        Git: count commits on default branch since yesterday (use local repo or GitHub API).
        CI: query CI system or use last build artifacts (GitHub Actions API or CI webhook status). Fallback: read status file if you already persist CI results.
        Tasks: query Task model counts and tasks_by_status; optionally group by tag.
        Jobs: query Celery/Background job metrics (if you store job results) or instrument key periodic tasks to write last_run/last_success.
        Errors: if you use Sentry, query Sentry API for count and top issues; otherwise parse recent logs for ERROR entries.
        DB/backup: run a simple DB size query and read last backup timestamp (from backup logs or file metadata).
    Collector code pattern: small functions per metric returning None or safe default on failure; collector composes them and writes a DailyHealth row.

    Scheduling

    If MissionControl already uses Celery + beat:
        Add a periodic task scheduled at 07:00 America/Denver (convert to UTC) that runs the collector and persists DailyHealth.
    If Celery is not used:
        Create a system cron job (or systemd timer) that calls a Django management command daily which runs the collector.
    Ensure the job runs once per Denver day and is idempotent (update the existing DailyHealth row for the date if present).

    UI

    Add a protected view (staff or a permission) at /admin/health-dashboard/ or /health-dashboard/.
    Components:
        Latest snapshot card with top-line summary (OK/warning/critical).
        Small panels: commits, CI status, open tasks, failing jobs, errors, DB/backup.
        7-day sparkline chart for commits, tasks, errors.
        Recent notes and link to full DailyHealth history page (paginated).
    Keep the UI minimal: server-side rendered templates with HTMX for small interactions (fits existing stack).

    Alerting & thresholds (optional MVP)

    For MVP, surface warnings in the dashboard (e.g., errors_count > X, failing_jobs > 0, last_backup > 24h).
    Later: add Telegram/Slack messages when thresholds breached. Require explicit opt-in before sending to external channels.

    Tests

    Unit tests for collectors (mock APIs and repo states).
    Integration test: run management command to create DailyHealth row and assert fields.
    UI tests: view access, expected panels render, basic smoke tests.

    Deployment & runbook

    Code review: create PR with model, migration, collector, management command or Celery task, view, and templates.
    Staging: deploy to staging and run scheduled job manually to verify collection.
    Verify data integrity and retention: check sample rows in staging DB.
    Production: deploy and enable scheduled job. Monitor the first 7 runs.
    Rollback plan: remove scheduled job and migration (reverse migration) if urgent.

    Security & privacy

    Only show dashboard to authenticated staff.
    Do not surface raw logs or sensitive payloads (mask or redact error traces if shown).
    Store only minimal error metadata (counts, short message snippet).
    Any external API tokens (GitHub, Sentry) should be stored in environment and not printed in UI or logs.

    Storage & retention

    Default retention: keep daily rows for 365 days, then aggregate older data to weekly or monthly summaries to keep table small.
    Add a simple management command to archive older rows or downsample.

    Observability & ops

    Add a small metric around collector success/failure (Prometheus counter or simple log lines).
    Send a daily collector run success/failure log to your existing logging pipeline.
    If using Celery, instrument runtimes and failures.

Implementation checklist (ready-to-run steps)

    Draft PR: add models.py changes + migration
    Implement collectors: module missioncontrol.health.collectors
    Add management command: python manage.py collect_daily_health
    Add Celery periodic task / cron entry (07:00 America/Denver)
    Create templates and view with permission checks
    Add unit & integration tests
    Review & merge PR to repo (I will not push without approval)
    Deploy to staging and run the command manually
    Verify, then enable scheduling in production
    After stable run: add alerts (Telegram/Slack) if desired

Example code snippets (sketch)

    Management command:
        loads Django, calls collector, writes/updates DailyHealth.objects.update_or_create(date=..., defaults=payload)
    Celery task:
        @shared_task def collect_daily_health_task(): run collector and save
    Collector:
        def collect_commits(): use gitpython or GitHub API
        def collect_tasks(): Task.objects.filter(...).aggregate(...)
        def collect_errors(): query Sentry or scan logs with safe time window

Optional enhancements (phase 2)

    Include lightweight authentication for stakeholders to subscribe to daily briefings via Telegram/email.
    Add role-based views and drill-downs (click a panel to see contributing issues).
    Auto-generate an EOD summary note and append to memory/YYYY-MM-DD.md (opt-in).

If you want, next I can:

    Draft the exact Django model and migration file content.
    Write the collector module skeleton and a management command in the workspace and show the diff for review.
    Or produce a PR checklist and a ready-to-run cron/Celery snippet targeted to your environment.

Which next step would you like me to take?
