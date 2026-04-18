# IIH Team Lead Daily Work Assistant

A **Microsoft 365 Copilot Declarative Agent** for IIH Team Leads.

This agent brings together the IIH service manual, your Workday job description duties, SharePoint, OneDrive, Outlook meetings, and Monarch work-management resources into a single AI assistant that can help you **execute daily tasks** directly inside Microsoft 365 Copilot (Teams, Outlook, SharePoint, etc.).

---

## What This Agent Does

| Capability | What the agent uses |
|---|---|
| 📖 IIH procedures & standards | IIH Service Manual on SharePoint |
| 📋 Task ownership & role clarity | Workday job-description duties |
| 📁 Team & project documents | SharePoint document libraries |
| 🗂️ Personal work files | OneDrive personal drive |
| 📅 Meetings & action items | Outlook Calendar via Microsoft Graph |
| 🔧 Daily work queue & tickets | Monarch (via Microsoft Graph Connector) |
| 🌐 External research | Bing web search |

### Example things you can ask

- *"Start my day — what do I have on today and what's outstanding in my queue?"*
- *"What does the IIH service manual say about escalating a P1 incident?"*
- *"Summarise my meetings this week and list any action items I own."*
- *"Find the latest SOP for [process name] in SharePoint and give me the key steps."*
- *"Help me write a status update for my team based on today's Monarch activity."*
- *"Create a Teams meeting invite for tomorrow at 10 AM with [attendees] about [topic]."*

---

## Repository Structure

```
appPackage/
  manifest.json              ← Teams App manifest (registers the agent in Microsoft 365)
  declarativeAgent.json      ← Declarative agent: knowledge sources, capabilities, conversation starters
  instruction.txt            ← Full system prompt / behaviour instructions for the agent
  outlookCalendarPlugin.json ← Plugin for reading & creating Outlook calendar events
  color.png                  ← App icon (192×192, Microsoft blue)
  outline.png                ← App outline icon (32×32, white)
README.md
```

---

## Setup & Deployment

### Prerequisites

- Microsoft 365 tenant with **Copilot for Microsoft 365** licenses.
- **Teams Toolkit** for VS Code (v5.10+) *or* the [Teams Developer Portal](https://dev.teams.microsoft.com).
- Admin access to register Graph Connectors (needed for Monarch).
- SharePoint site URLs for the IIH site, Monarch site, and your OneDrive.

### Step 1 — Configure placeholder values

Open `appPackage/manifest.json` and `appPackage/declarativeAgent.json` and replace every `<PLACEHOLDER>` with your real values:

| Placeholder | What to replace it with |
|---|---|
| `<YOUR_TENANT>` | Your Microsoft 365 tenant name (e.g. `contoso`) |
| `<IIH_SITE>` | SharePoint site path for the IIH team site (e.g. `IIH-Operations`) |
| `<MONARCH_SITE>` | SharePoint site path where Monarch documents live |
| `<YOUR_UPN>` | Your user principal name, underscores instead of `@` and `.` (e.g. `john_smith_contoso_com`) |
| `<MONARCH_GRAPH_CONNECTOR_ID>` | The Graph Connector connection ID for Monarch (see Step 3) |
| `${{TEAMS_APP_ID}}` | Leave this as-is if using Teams Toolkit; replace with a GUID if sideloading manually |

### Step 2 — Add the IIH Service Manual URL

In `appPackage/declarativeAgent.json`, add the direct URL to the IIH Service Manual document or library under `capabilities[0].items_by_url`:

```json
{
  "url": "https://<YOUR_TENANT>.sharepoint.com/sites/<IIH_SITE>/Shared%20Documents/IIH-Service-Manual"
}
```

### Step 3 — Register the Monarch Graph Connector (optional but recommended)

If Monarch exposes an API or has an existing Microsoft Graph Connector:

1. Open the [Microsoft 365 Admin Center](https://admin.microsoft.com) → **Settings → Search & intelligence → Data sources**.
2. Add the Monarch connector and note the **Connection ID**.
3. Paste the Connection ID into `declarativeAgent.json` under `capabilities[2].connections[0].connection_id`.

If Monarch is not yet connected, remove the `GraphConnectors` block from `declarativeAgent.json` for now — the agent will still work with SharePoint/OneDrive and web search.

### Step 4 — Deploy the Teams App

**Option A — Teams Toolkit (recommended):**
1. Open this folder in VS Code with Teams Toolkit installed.
2. Sign in to your Microsoft 365 account in Teams Toolkit.
3. Click **Provision** → **Deploy** → **Publish to Org**.

**Option B — Manual sideload:**
1. Zip the entire `appPackage/` folder contents (not the folder itself) into `IIHTeamLeadAssistant.zip`.
2. Go to [Teams Admin Center](https://admin.teams.microsoft.com) → **Teams apps → Manage apps → Upload**.
3. Upload the zip and approve it for your organisation.

### Step 5 — Test the agent

1. Open Microsoft Teams → **Copilot** (left sidebar).
2. Click the agent selector (top of Copilot chat) and choose **IIH TL Assistant**.
3. Try one of the conversation starters or type a question.

---

## Customisation

- **Add more SharePoint libraries**: Add entries to `capabilities[0].items_by_url` in `declarativeAgent.json`.
- **Change the system prompt**: Edit `appPackage/instruction.txt` — no manifest changes needed.
- **Add more plugins/actions**: Create a new `*Plugin.json` file following the pattern in `outlookCalendarPlugin.json` and reference it in the `actions` array of `declarativeAgent.json`.
- **Update the app icon**: Replace `color.png` (192×192) and `outline.png` (32×32) with your own images.

---

## Contributing

1. Fork this repository.
2. Make changes in a feature branch.
3. Open a pull request with a description of what changed and why.

