Downloads Audit Pipeline
Automated daily audit of a Windows Downloads folder from WSL — read-only, nothing is ever moved or deleted.
What it does

Scans the Downloads folder every day at 8am via cron
Collects filename, file type, size, date modified, and age for every file
Detects new files, files over 1GB, and files older than 365 days
Logs results to a local PostgreSQL database
Generates a visual report (pie chart, bar chart, oldest and biggest files)
Sends an email via AWS SES with the chart attached when notable events occur
Sends a full monthly report on the 1st of every month
Logs every run to AWS CloudWatch

Email triggers
TriggerFrequencyNew file downloadedDaily (when it happens)File over 1 GBDaily (when it happens)File older than 365 daysDaily (when it happens)Full monthly report1st of every month
Stack
ToolRolePythonCore scan scriptCron (WSL)Runs automatically every day at 8amPostgreSQL (local)Logs every file and scan summaryAWS SESSends email with chart attachedAWS CloudWatchLogs every runJupyter NotebookInteractive visual reportGitHub ActionsCI — syntax check on every push
Security

Read-only — no files are ever modified, moved, or deleted
All credentials stored in .env (never committed)
safe_path() validates every file path before access
No recursion — only scans top-level Downloads folder

Project Structure
```
downloads-audit-pipeline/
├── daily_scan.py
├── downloads_audit.ipynb
├── create_notebook.py
├── .env.example
├── .gitignore
└── reports/
```
How to run

Copy .env.example to .env and fill in your credentials
Run manually: python3 daily_scan.py
Schedule with cron: 0 8 * * * /usr/bin/python3 /path/to/daily_scan.py

Author
Juan Spinelli — GitHub · LinkedIn

