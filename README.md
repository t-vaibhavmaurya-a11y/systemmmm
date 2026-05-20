# jira-worklog-action

Scheduled **Jira worklog export** matching your local `worklog` helper (`jira_worklog.py --by-day --days 14`, same script pattern as `fse-mgmt/scripts/jira-aliases.sh`).

This repo runs the same exporter on GitHub Actions: **cron** + **manual runs**, uploads **artifacts** (`worklog-by-day.txt`, `issues-summary.csv`).

## Repository secrets

GitHub → **Settings → Secrets and variables → Actions**, add:

| Secret | Description |
|--------|--------------|
| `JIRA_SITE_URL` | Base URL, e.g. `https://your-domain.atlassian.net` |
| `JIRA_USER_EMAIL` | Login email tied to the API token |
| `JIRA_API_TOKEN` | [Atlassian API token](https://id.atlassian.com/manage-profile/security/api-tokens) |

Optional:

| Secret | When |
|--------|------|
| `REQUEST_VERIFY_SSL` | Set to `false` only if you must disable TLS verify (corporate proxy / bad cert). |

The script uses `os.environ` first; `~/.cursor/credentials.env` is only on your laptop, not in Actions.

## Schedule

The workflow uses **UTC** (`cron` in `.github/workflows/worklog-report.yml`). Edit the `schedule.cron` line to change when it runs.

Defaults for scheduled runs: **14 days** lookback, **Asia/Kolkata** for day boundaries. To change those defaults, edit the `else` branch in the “Resolve parameters” step.

**Run manually:** Actions → **Jira worklog export** → **Run workflow** (choose days and timezone).

## Results

After each run, open the workflow run → **Artifacts** → download the zip. Inside:

- `worklog-by-day.txt` — same style as `--by-day` locally  
- `issues-summary.csv` — issue-level export for the same window  

## Local run (same as before)

```bash
pip install -r requirements.txt
export JIRA_SITE_URL=... JIRA_USER_EMAIL=... JIRA_API_TOKEN=...
python jira_worklog.py --by-day --days 14 --timezone Asia/Kolkata
```

## This project’s GitHub repo

Upstream remote: **[t-vaibhavmaurya-a11y/systemmmm](https://github.com/t-vaibhavmaurya-a11y/systemmmm)** (`https://github.com/t-vaibhavmaurya-a11y/systemmmm.git`).

Local clone layout (already initialized in dev):

```bash
cd ~/dev/jira-worklog-action   # or your path
git remote add origin https://github.com/t-vaibhavmaurya-a11y/systemmmm.git   # skip if origin exists
git push -u origin main
```

If `git push` fails with **403 Permission denied** to another username, HTTPS is using cached credentials for the wrong GitHub account. Fix by signing in as **t-vaibhavmaurya-a11y** ([HTTPS + PAT](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)) or switch the remote to SSH and use the key tied to that account (`git remote set-url origin git@github.com:t-vaibhavmaurya-a11y/systemmmm.git`).

Then add the secrets above and confirm a **workflow_dispatch** run succeeds before relying on cron.
