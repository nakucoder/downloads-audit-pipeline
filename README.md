# Downloads Audit Pipeline

Automated daily audit of a Windows Downloads folder from WSL — read-only, nothing is ever moved or deleted.

## What it does

- Scans the Downloads folder every day at 8am via cron
- Collects filename, file type, size, date modified, and age for every file
- Flags files over 1 GB and files older than 365 days
- Logs results to a local PostgreSQL database
- Saves a visual report (charts + tables) as a PNG
- Sends an email alert via AWS SNS when notable files are found
- Logs every run to AWS CloudWatch

## Stack

| Tool | Role |
|------|------|
| Python | Core scan script |
| Cron (WSL) | Runs automatically every day at 8am |
| PostgreSQL (local) | Logs every file and scan summary |
| AWS SNS | Email alerts for notable files |
| AWS CloudWatch | Observability — logs every run |
| Jupyter Notebook | Interactive visual report |
| GitHub Actions | CI - syntax check on every push |

## Security

- Read-only — no files are ever modified, moved, or deleted
- All credentials stored in `.env` (never committed)
- `safe_path()` validates every file path before access
- No recursion — only scans top-level Downloads folder

## Project Structure

downloads-audit-pipeline/
├── daily_scan.py        # Main script — scan, log, alert
├── downloads_audit.ipynb # Interactive Jupyter report
├── create_notebook.py   # Generates the notebook file
├── .gitignore
└── .env                 # Credentials (not committed)

## How to run

1. Copy `.env.example` and fill in your credentials
2. Run manually: `python3 daily_scan.py`
3. Schedule with cron: `0 8 * * * /usr/bin/python3 /path/to/daily_scan.py`

## Author

Juan Spinelli — [GitHub](https://github.com/nakucoder) · [LinkedIn](https://linkedin.com/in/juan-spinelli-85b6a1294)
