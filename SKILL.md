---
name: gsheets
description: Read, write, and manage Google Sheets directly from Claude Code. Service account auth, table formatting, CSV import/export, formatting, search.
---

# Google Sheets Skill

Read, write, and manage Google Sheets directly from Claude Code.

## When to Use This Skill

Trigger when user:
- Mentions Google Sheets, spreadsheet, or gsheets
- Wants to read/write/update a Google Sheet
- References a spreadsheet ID or Google Sheets URL
- Wants to export data to a sheet or import from one
- Asks to format, clear, or search within a spreadsheet

## Critical: Always Use run.py Wrapper

NEVER call scripts directly. ALWAYS use `python scripts/run.py [script]`:

```bash
# All commands must be run from the skill directory:
cd C:\Users\Privat\.claude\skills\gsheets

# Auth
python scripts/run.py auth.py status
python scripts/run.py auth.py set-key C:/path/to/key.json

# Read data (default: formatted table)
python scripts/run.py gsheets.py read --spreadsheet-id ID --range "Sheet1!A1:Z100"
python scripts/run.py gsheets.py read --spreadsheet-id ID --range "Sheet1!A1:Z100" --json
python scripts/run.py gsheets.py read --spreadsheet-id ID --range "Sheet1!A1:Z100" --csv

# Write data
python scripts/run.py gsheets.py write --spreadsheet-id ID --range "Sheet1!A1" --values '[["a","b"],["c","d"]]'
python scripts/run.py gsheets.py write --spreadsheet-id ID --range "Sheet1!A1" --csv-file /path/to/data.csv
python scripts/run.py gsheets.py write --spreadsheet-id ID --range "Sheet1!A1" --values '[["formula"]]' --value-input-option USER_ENTERED

# Append rows
python scripts/run.py gsheets.py append --spreadsheet-id ID --sheet-name "Sheet1" --values '[["new","row"]]'

# Sheet management
python scripts/run.py gsheets.py list-sheets --spreadsheet-id ID
python scripts/run.py gsheets.py create-sheet --spreadsheet-id ID --sheet-name "New Tab"
python scripts/run.py gsheets.py info --spreadsheet-id ID

# Clear data
python scripts/run.py gsheets.py clear --spreadsheet-id ID --range "Sheet1!A1:Z100"

# Format cells
python scripts/run.py gsheets.py format --spreadsheet-id ID --range "Sheet1!A1:Z1" --bold --bg-color "#333333" --text-color "#FFFFFF"
python scripts/run.py gsheets.py format --spreadsheet-id ID --range "Sheet1!A1:Z1" --italic --font-size 14 --h-align center

# Search
python scripts/run.py gsheets.py find --spreadsheet-id ID --sheet-name "Sheet1" --query "search term"

# Get URL
python scripts/run.py gsheets.py get-url --spreadsheet-id ID
```

## Known Spreadsheets

| Name | ID | Purpose |
|------|-----|---------|
| Salvora Prehooks | 13y_rw5s_7FlVhCHhKr0C9w7oPg9AWW9nPFRUG2YXJEU | Prehook image and video prompts |

## Core Workflow

### Step 1: Check Auth
```bash
cd C:\Users\Privat\.claude\skills\gsheets
python scripts/run.py auth.py status
```
If auth is not configured, the status command shows the service account email. The user must share their spreadsheet with that email address.

### Step 2: Read Data
```bash
python scripts/run.py gsheets.py read --spreadsheet-id 13y_rw5s_7FlVhCHhKr0C9w7oPg9AWW9nPFRUG2YXJEU --range "Sheet1!A1:D10"
```
Default output is a formatted table. Add `--json` for raw JSON or `--csv` for CSV format.

### Step 3: Write Data
Values must be a JSON array of arrays (rows of cells):
```bash
python scripts/run.py gsheets.py write --spreadsheet-id ID --range "Sheet1!A1" --values '[["Name","Score"],["Alice","95"],["Bob","87"]]'
```

To upload from a CSV file:
```bash
python scripts/run.py gsheets.py write --spreadsheet-id ID --range "Sheet1!A1" --csv-file /path/to/data.csv
```

Use `--value-input-option USER_ENTERED` if values contain formulas (e.g. `=SUM(A1:A10)`).

### Step 4: Search
```bash
python scripts/run.py gsheets.py find --spreadsheet-id ID --sheet-name "Sheet1" --query "search term"
```
Returns cell references and values for all matches.

## Tips
- **Spreadsheet ID** is the long string in the Google Sheets URL between `/d/` and `/edit`
- **Range format**: `"SheetName!A1:Z100"` or just `"A1:Z100"` for the first sheet
- **Values** for write/append must be JSON array of arrays
- The **service account email** must be shared (as Editor) on any spreadsheet you want to access
- Use `list-sheets` to see all tab names before reading/writing
- Use `info` for a quick overview of the spreadsheet structure
- For formulas, use `--value-input-option USER_ENTERED`
- The skill auto-creates its virtual environment on first run
