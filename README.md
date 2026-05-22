# Downloads Audit Pipeline

Automated daily audit of a Windows Downloads folder from WSL — read-only, nothing is ever moved or deleted.

## What it does

- Scans the Downloads folder every day at 8am via Windows Task Scheduler
- Collects filename, file type, size, date modified, and age for every file
- Detects new files, files over 1GB, and files older than 365 days
- Logs results to a local PostgreSQL database
- Generates a visual report (pie chart, bar chart, oldest and biggest files)
- Sends an email via AWS SES with the chart attached when notable events occur
- Sends a full monthly report on the 1st of every month
- Logs every run to AWS CloudWatch

## Email triggers

| Trigger | Frequency |
|---------|----------|
| New file downloaded | Daily (detected at 8am) |
| File over 1 GB | Daily (detected at 8am) |
| File older than 365 days | Daily (detected at 8am) |
| Full monthly report | 1st of every month |

## Stack

| Tool | Role |
|------|------|
| Python | Core scan script |
| Windows Task Scheduler | Runs automatically every day at 8am — no WSL terminal needed |
| PostgreSQL (local) | Logs every file and scan summary |
| AWS SES | Sends email with chart attached |
| AWS CloudWatch | Logs every run |
| Jupyter Notebook | Interactive visual report |
| GitHub Actions | CI — syntax check on every push |

## Scheduling

The script runs via **Windows Task Scheduler** (not cron). This means:
- No WSL terminal needs to be open
- Runs automatically at 8am every day
- If the PC was off or asleep at 8am, it runs as soon as the PC wakes up (`StartWhenAvailable`)

To view or edit the task: open Task Scheduler → find "Downloads Audit Pipeline".

To recreate the task (e.g. on a new machine), run this in PowerShell as your user:

```powershell
$action = New-ScheduledTaskAction -Execute "wsl.exe" -Argument "-d Ubuntu -- /usr/bin/python3 /home/juana/downloads-audit-pipeline/daily_scan.py"
$trigger = New-ScheduledTaskTrigger -Daily -At "8:00AM"
$settings = New-ScheduledTaskSettingsSet -StartWhenAvailable -RunOnlyIfNetworkAvailable -ExecutionTimeLimit (New-TimeSpan -Minutes 10)
$principal = New-ScheduledTaskPrincipal -UserId $env:USERNAME -LogonType Interactive -RunLevel Limited
Register-ScheduledTask -TaskName "Downloads Audit Pipeline" -Action $action -Trigger $trigger -Settings $settings -Principal $principal -Description "Daily downloads audit"
```

## Security

- Read-only — no files are ever modified, moved, or deleted
- All credentials stored in `.env` (never committed)
- `safe_path()` validates every file path before access
- No recursion — only scans top-level Downloads folder

## Project Structure

```
downloads-audit-pipeline/
├── daily_scan.py
├── downloads_audit.ipynb
├── create_notebook.py
├── .env.example
├── .gitignore
└── reports/
```

## How to run manually

1. Copy `.env.example` to `.env` and fill in your credentials
2. Run: `python3 /home/juana/downloads-audit-pipeline/daily_scan.py`

## Author

Juan Spinelli — [GitHub](https://github.com/nakucoder) · [LinkedIn](https://linkedin.com/in/juan-spinelli-85b6a1294)
