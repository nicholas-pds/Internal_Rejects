# Internal Rejects Data Pipeline

Automatically pulls rejected cases from the database, loads the data into a Google Sheet, and syncs to a Looker Studio dashboard.

## Overview

This project automates a daily workflow to:
1. Query rejected cases from SQL Server database
2. Load the data into Google Sheets
3. Display the data in a Looker Studio dashboard for analysis

## Project Structure

```
Internal_Rejects/
├── main.py                          # Entry point for running the pipeline
├── pyproject.toml                   # Project dependencies and configuration
├── LICENSE                          # Project license
├── README.md                         # This file
├── sql_query/
│   └── internal_rejects_1.sql       # SQL query to fetch rejected cases (last 184 days)
└── src/
    ├── __init__.py
    ├── main.py                      # Main orchestration logic
    ├── db_handler.py                # SQL Server database connection and query execution
    └── sheets_handler.py            # Google Sheets API integration
```

## Features

- **Database Integration**: Connects to SQL Server and executes stored queries
- **Data Processing**: Converts SQL results to pandas DataFrame
- **Google Sheets Sync**: Automatically uploads data to specified Google Sheet
- **Looker Studio Compatible**: Column naming optimized for Looker Studio dashboards

## Prerequisites

### Required Software
- Python 3.13+
- SQL Server ODBC Driver 17 (or compatible)
- Google Cloud Service Account with Sheets and Drive API access

### Required Environment Variables

Create a `.env` file in the project root with the following:

```
# SQL Server Credentials
SQL_SERVER=your_server_name
SQL_DATABASE=your_database_name
SQL_USERNAME=your_sql_username
SQL_PASSWORD=your_sql_password

# Google Sheets Credentials (full JSON as string)
GOOGLE_SERVICE_ACCOUNT_JSON={"type":"service_account","project_id":"..."}
```

## Installation

1. Clone the repository:
```bash
git clone https://github.com/nicholas-pds/Internal_Rejects.git
cd Internal_Rejects
```

2. Install dependencies:
```bash
pip install -e .
```

Or install manually:
```bash
pip install dotenv google-auth gspread gspread-dataframe oauth2client pandas pyodbc
```

3. Configure environment variables:
   - Update `.env` with your SQL Server and Google Sheets credentials

4. Set Google Sheets target:
   - Update the `SHEET_NAME` variable in `src/main.py` to match your target Google Sheet tab

## Usage

Run the pipeline:
```bash
python main.py
```

Or from the src directory:
```bash
python -m src.main
```

## How It Works

### 1. Database Query (`sql_query/internal_rejects_1.sql`)
- Queries the `cases` and `casetaskshistory` tables
- Filters for rejected cases from the last 184 days
- Returns columns formatted for Looker Studio

### 2. Database Handler (`src/db_handler.py`)
- Reads SQL query from file
- Connects to SQL Server using pyodbc
- Executes query and returns pandas DataFrame

### 3. Sheets Handler (`src/sheets_handler.py`)
- Authenticates with Google Sheets API using service account
- Uploads DataFrame to specified Google Sheet
- Clears existing data before writing new results

### 4. Main Orchestration (`src/main.py`)
- Coordinates the pipeline steps
- Handles errors and logging
- Provides user feedback

## Scheduling

To run this automatically on a schedule, use:

### Windows Task Scheduler
1. Open Task Scheduler
2. Create Basic Task with trigger (daily, hourly, etc.)
3. Set action to run: `python.exe` with arguments: `C:\path\to\Internal_Rejects\main.py`

### Alternative: Cron Job (Linux/Mac)
```bash
0 8 * * * /usr/bin/python3 /path/to/Internal_Rejects/main.py
```

## Configuration

### SQL Query
Edit `sql_query/internal_rejects_1.sql` to modify:
- Time range (currently 184 days)
- Columns returned
- Filter conditions

### Target Sheet
In `src/main.py`, update:
```python
SHEET_NAME = "Your_Sheet_Tab_Name"
```

## Troubleshooting

**SQL Connection Error:**
- Verify ODBC driver is installed: `odbcinst -j`
- Check SQL Server credentials and network access
- Ensure firewall allows database connections

**Google Sheets Authentication Error:**
- Validate service account JSON is properly formatted
- Ensure service account email has access to target Google Sheet
- Check that Sheets API is enabled in Google Cloud Project

**File Not Found Error:**
- Verify `internal_rejects_1.sql` exists in `sql_query/` directory
- Check file paths are correct in `src/main.py`

## Dependencies

| Package | Purpose |
|---------|---------|
| `pandas` | Data manipulation and DataFrame handling |
| `pyodbc` | SQL Server database connection |
| `gspread` | Google Sheets API client |
| `gspread-dataframe` | DataFrame to Google Sheets conversion |
| `google-auth` | Google authentication |
| `python-dotenv` | Environment variable loading |

## License

See LICENSE file for details.

## Support

For issues or questions, please open an issue in the GitHub repository.
