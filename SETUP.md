# Deploying to VS Code Copilot

A step-by-step guide to running this MCP server inside VS Code Copilot Chat.

> See the [README](README.md) for the project description and usage examples.

## Prerequisites

- **VS Code** with the **GitHub Copilot** and **GitHub Copilot Chat** extensions
- **Python 3.10+**
- An **ArcGIS Online or Enterprise account** using built-in credentials
- Patience ;-)

---

## Step 1 — Clone the project

**Ensure you clone from vSE Branch, NOT master**

Use either method:

```bash
git clone -b vSE https://github.com/YOURNAME/arcgis-portal-mcp-1.git
```

Or clone through **GitHub Desktop**.

## Step 2 — Open the project in VS Code

**File → Open Folder** → select the project root (the folder containing `pyproject.toml`).

## Step 3 — Open a terminal

**Ctrl + Shift + `** — or use the Terminal menu in the top ribbon.

## Step 4 — Create the virtual environment

```powershell
python -m venv .venv
```

> **Do not connect to ArcGIS Pro Python interpreter**
> Use this instead. It builds an isolated environment *from* Pro's interpreter — packages install into `.venv` (ensures easy and stress free testing):
>
> ```powershell
> & "C:\Program Files\ArcGIS\Pro\bin\Python\envs\arcgispro-py3\python.exe" -m venv .venv
> ```

## Step 5 — Install the project and its dependencies

```powershell
.\.venv\Scripts\pip.exe install -e .
```
**YES YOU NEED THE PERIOD AFTER THE e**

Dependencies are resolved from `pyproject.toml`.

## Step 6 — Verify the install

Both commands should print `ok`:

```powershell
.\.venv\Scripts\python.exe -c "import arcgis_portal_mcp, mcp; print('ok')"
.\.venv\Scripts\python.exe -c "from mcp.server.fastmcp import FastMCP; print('ok')"
```

> If the second command fails with `No module named 'mcp.server.fastmcp'`, you have `mcp` 2.0+, Run `.\.venv\Scripts\pip.exe install "mcp<2"`.

## Step 7 — Add your credentials

Open **`.env.example`** and fill in your portal URL and built-in user credentials.

Then **File → Save As**:

| Field | Value |
|---|---|
| File name | `.env` |
| Save as type | **All Files (\*.\*)** |
| Location | Project root — beside `pyproject.toml`, **not** inside `arcgis_portal_mcp/` |

> Setting **All Files** matters — **YOU DO NOT WANT** `.env.txt`.

## Step 8 — Verify the server connects

```powershell
.\.venv\Scripts\python.exe -m arcgis_portal_mcp
```

You should see:

```
Loading env from ...\.env
Auto-connected to yourorg.maps.arcgis.com
Ready, waiting for connect_portal tool call
```

Once verified... **Press Ctrl + C to stop it.**

> Again...  This step is just to verify

## Step 9 — Register the server with VS Code

**Ctrl + Shift + P** → type **`MCP: Open User Configuration`**

Paste the following, replace the path on the command line with your own:

```json
{
  "servers": {
    "arcgis-portal": {
      "type": "stdio",
      "command": "C:\\Users\\Miko\\Documents\\GitHub\\arcgis-portal-mcp\\.venv\\Scripts\\python.exe",
      "args": ["-m", "arcgis_portal_mcp"]
    }
  }
}
```

> **Make sure** paths have **double backslashes**

## Step 10 — Start the server

Save the file. A **Start** link appears above the `"arcgis-portal"` block. Click it.

## Step 11 — Use it

1. Open Copilot Chat — **Ctrl + Alt + I**
2. Ask a question:

   > Please identify all feature layers with *xyz* in their title

> **Before working against a live org**, use the Tools panel to disable `delete_item`, `delete_features`, `batch_delete_items`, and `batch_update_items`.

---

## Troubleshooting

| Symptom | Cause | Fix |
|---|---|---|
| `No module named arcgis_portal_mcp` | `command` points at the wrong interpreter | Use the `.venv\Scripts\python.exe` path |
| `No module named 'mcp.server.fastmcp'` | `mcp` 2.0+ installed | `pip install "mcp<2"` |
| `Auto-connect skipped: no portal_url` | `.env` missing or misplaced | Must be in project root; check it isn't `.env.txt` |
| Server runs, no tools in chat | Chat is in Ask mode | Switch to **Agent** |
| Tools worked, now every call fails | Token expired after 2 hours | Click **Restart** in `mcp.json` |

**To see the actual error:** Ctrl + Shift + P → **`MCP: List Servers`** → select the server → **Show Output**.
