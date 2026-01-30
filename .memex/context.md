# Application Setup
- Update the following files to describe current application
- `pyproject.toml` - `name` and `description`
- `dist/index.html` - page `<title>` and heading
- `README.md`

# How to run Data App
Use `start.sh`
Start script runs `uv sync` and `uv run`

# GitHub Setup
If a GitHub Connector is available (listed in "Available Connectors" in system prompt), use it. Otherwise, prompt the user to set up a GitHub Connector in Hub (instead of asking for secret directly).

When adding a git remote, ALWAYS use HTTPS URL with token (never SSH):

1. **Find the connector's secret key**: Check "Available Connectors" in system prompt for the exact secret key name (e.g., "PREFIX_GITHUB_TOKEN")
2. **Use that exact secret key**: Replace `{GITHUB_CONNECTOR_TOKEN}` with the actual secret key from step 1

```bash
git remote add origin https://[GITHUB_CONNECTOR_TOKEN]@github.com/[REPO-OWNER]/[REPO-NAME]
```

# Data Sources

## CRITICAL: Read Before Implementation

1. **ALWAYS check this context.md file before implementing any feature**
2. **NEVER implement custom solutions for patterns documented here**

## Data Source Implementation - MANDATORY PATTERN

### When implementing ANY data source:
1. Available connectors are listed in the system prompt under "Available Connectors" with their type and secret keys that match environment variables.
2. Use the connector-specific code pattern below based on the connector type

## Secret/Credential Access

✅ Correct: `spreadsheet_url = os.environ.get("CONNECTORPREFIX_SPREADSHEET")`

## File Uploads

When the user mentions a file using the `@filename` syntax (file-mention), the agent must:

1. Extract the file label and path from the mention
2. Load the file using pandas or appropriate library:
   ```python
   import pandas as pd

   path = "/data/users/.../uploads/{my file name.csv}"
   df = pd.read_csv(path)
   ```

### File mention format
File mentions appear in prompts as:
```html
<span data-file-mention="" class="file-mention" data-id="..." data-label="my file name.csv.csv" data-path="/data/users/.../uploads/my file name.csv.csv">my file name.csv.csv</span>
```

Extract:
- **data-label**: Use as the key
- **data-path**: Use as the value (the actual file path)

**Important**: The `get_oauth_access_token()` function in `secrets_utils.py` fetches a fresh access token (~1 hour lifetime) from the Modal backend.
Use `os.environ.get("CONNECTORPREFIX_CONNECTOR_ID")` to get the connector ID.
The `MEMEX_DEPLOYMENT_TOKEN` and `MEMEX_BACKEND_URL` environment variables are auto-injected when the deployment includes OAuth connectors.


