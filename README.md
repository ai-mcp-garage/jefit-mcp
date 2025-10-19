# JEFit MCP Server

MCP server for analyzing JEFit workout data. Provides tools to list workout dates and retrieve detailed workout information.

## Setup

1. **Install dependencies:**
   ```bash
   uv sync
   ```

2. **Configure environment variables:**
   
   Set the following environment variables or use your secrets manager of choice.
   ```
   JEFIT_USERNAME=your_username
   JEFIT_PASSWORD=your_password
   JEFIT_TIMEZONE=-07:00
   ```
   
   Note: Use timezone offset format like `-07:00` for PDT, `-04:00` for EDT

The exercise database will be automatically fetched and cached on first startup.

## MCP Configuration

### Local/stdio Configuration (Recommended)

Add to your MCP client configuration:

```json
{
  "mcpServers": {
    "jefitWorkouts": {
      "type": "stdio",
      "command": "uv",
      "args": ["run", "--directory", "/path/to/jefit-mcp", "python", "server.py"]
    }
  }
}
```

### Configuration Locations

- **Cursor**: `.cursor/mcp.json` (project) or `~/.cursor/mcp.json` (user)
- **Claude Desktop**: `~/Library/Application Support/Claude/claude_desktop_config.json`
- **VS Code**: `.vscode/mcp.json`

## Available Tools

### 1. `list_workout_dates`

List all workout dates within a date range.

**Parameters:**
- `start_date` (required): Start date in YYYY-MM-DD format
- `end_date` (optional): End date in YYYY-MM-DD format (defaults to today)

**Returns:** List of workout dates

**Example:**
```json
{
  "start_date": "2025-10-01",
  "end_date": "2025-10-19"
}
```

### 2. `get_workout_info`

Get detailed workout information for a specific date.

**Parameters:**
- `date` (required): Date in YYYY-MM-DD format

**Returns:** Markdown-formatted workout details including:
- Start time and duration
- Total weight lifted
- Exercise list with muscle groups, equipment, sets, and reps

**Example:**
```json
{
  "date": "2025-10-17"
}
```

### 3. `get_batch_workouts`

Get detailed workout information for multiple dates in a single call.

**Parameters:**
- `dates` (required): List of dates in YYYY-MM-DD format

**Returns:** Markdown-formatted workout details for all requested dates, separated by horizontal rules

**Example:**
```json
{
  "dates": ["2025-10-15", "2025-10-17", "2025-10-19"]
}
```

## Testing

Run the test script to verify everything works:

```bash
uv run python scripts/test_server.py
```

## Project Structure

```
jefit-mcp/
├── server.py              # Main MCP server
├── auth.py                # JEFit authentication
├── history.py             # Workout history fetching
├── workout_info.py        # Workout details and formatting
├── utils.py               # Utility functions
├── rsc_base.py           # React Server Components parser
├── data/
│   └── exercises_db.json  # Exercise database cache
└── scripts/
    ├── test_server.py     # Server testing script
    └── update_exercise_db.py  # Exercise database updater
```

## Development

The server uses FastMCP 2.12+ and supports both stdio and HTTP transports. By default, it runs in stdio mode. To run in HTTP mode, set the `HOST` and `PORT` environment variables.

