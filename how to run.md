# How to run Notion Exporter on Windows

This guide runs the exporter from this project folder in PowerShell and saves your Notion backup under:

notion-exporter\exports\

Each run creates a timestamped folder inside exports, for example:

exports\2026-11-13T14-00-00Z\

A backup includes raw JSON, Markdown, HTML, and downloaded images/files.

## 1. Open PowerShell in the project folder

```powershell
cd "New folder\notion-exporter"
```

## 2. Check Node.js and pnpm

The project requires Node.js **22.19 or newer** and pnpm **9 or newer**.

```powershell
node --version
pnpm --version
```

If pnpm is not installed, enable Corepack and activate pnpm:

```powershell
corepack enable
corepack prepare pnpm@latest --activate
```

If Node.js is missing or older than 22.19, install a current Node.js LTS release, reopen PowerShell, then check the version again.

## 3. Install project dependencies

From the project folder, run:

```powershell
pnpm install
```

## 4. Create a Notion integration

1. Go to [Notion integrations](https://www.notion.so/my-integrations) and create an **internal integration**.
2. Copy the integration secret. Treat it like a password.
3. In Notion, open the page or teamspace you want to export. Choose **··· → Connections** and add your integration. The integration can only export pages it can access.

## 5. Add the integration token

Create your local environment file from the example, then open it:

```powershell
Copy-Item .env.example .env
notepad .env
```

Set the token line to your actual secret, then save and close Notepad:

```dotenv
NOTION_TOKEN=secret_your_token_here
```

Do not share or commit your token. The .env file is intended to remain local.

## 6. Check that Notion access works

```powershell
pnpm dev check
```

If it reports **0 visible objects**, return to Notion and connect the integration to the page/teamspace you want to export. Then run the check again.

## 7. Export your Notion workspace

```powershell
pnpm dev export
```

The command writes the export beneath:

notion-exporter\exports\

It creates a new timestamped subfolder each run. The first export can take a long time for a large workspace, so leave PowerShell open until the command finishes. Later runs are usually faster because the exporter uses incremental updates.

## Useful export commands

Choose a different output folder:

```powershell
pnpm dev export --out "D:\NotionBackup"
```

Do a full fresh export, fetching all pages again:

```powershell
pnpm dev export --force
```

List visible object IDs without writing an export:

```powershell
pnpm dev export --dry-run
```

## Troubleshooting

### Client export error from @notionhq/client

If you see an error like “The requested module '@notionhq/client' does not provide an export named 'Client'”, confirm you are running the latest project files and run:

```powershell
pnpm install
pnpm dev check
```

### Zero visible objects

The integration token may be valid, but the integration has not been connected to any pages. In Notion, add it under **··· → Connections** on the page or teamspace, then retry pnpm dev check.

### Token/authentication error

Reopen .env and confirm NOTION_TOKEN contains the integration's secret, with no placeholder text or accidental spaces. Ensure the integration is connected to the page you want to export.

### Command fails immediately

Make sure PowerShell is in the project directory and use the commands exactly as shown, without an extra trailing backslash:

```powershell
cd "notion-exporter"
pnpm dev check
```

## Export contents

Inside the timestamped export folder, you will find:

- raw\ — Notion data as JSON.
- markdown\ — readable Markdown pages.
- html\ — browsable static HTML archive.
- assets\ — downloaded images and files.
- manifest.json and state.json — export metadata and incremental/resume state.
