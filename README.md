# Atlassian Skill

Jira + Confluence integration for AI coding assistants with dual auth support (OAuth 2.1 via MCP server or API token fallback).

## Supported AI Clients

<p align="center">
  <a href="#claude-code"><img src="https://img.shields.io/badge/Claude_Code-D97757?style=for-the-badge&logo=anthropic&logoColor=white" alt="Claude Code" /></a>
  <a href="#gemini-cli--cursor--codex--goose"><img src="https://img.shields.io/badge/Gemini_CLI-8E75B2?style=for-the-badge&logo=google&logoColor=white" alt="Gemini CLI" /></a>
  <a href="#gemini-cli--cursor--codex--goose"><img src="https://img.shields.io/badge/Antigravity-4285F4?style=for-the-badge&logo=google&logoColor=white" alt="Google Antigravity" /></a>
  <a href="#gemini-cli--cursor--codex--goose"><img src="https://img.shields.io/badge/Cursor-000000?style=for-the-badge&logo=cursor&logoColor=white" alt="Cursor" /></a>
  <a href="#gemini-cli--cursor--codex--goose"><img src="https://img.shields.io/badge/OpenAI_Codex-412991?style=for-the-badge&logo=openai&logoColor=white" alt="OpenAI Codex" /></a>
  <a href="#gemini-cli--cursor--codex--goose"><img src="https://img.shields.io/badge/Goose-FF6B35?style=for-the-badge&logo=go&logoColor=white" alt="Goose" /></a>
</p>

## Features

**Jira**
- Search issues with JQL queries (full filtering, sorting, pagination)
- Create, update, and transition issues through workflow statuses
- Add and list comments on issues
- List accessible projects and available statuses per project

**Confluence**
- Search pages with CQL queries or free-text search
- Read, create, and update wiki pages with HTML content
- Browse spaces and page hierarchies (parent/child navigation)
- List all spaces with metadata

**Authentication**
- **OAuth 2.1** via Atlassian MCP server (recommended) -- browser-based consent, PKCE, auto-refresh tokens
- **API token** fallback -- email + token stored securely in system keyring
- Auto-detects backend: OAuth uses MCP tools, API token uses REST API directly
- Secure credential storage via system keyring (macOS Keychain, Windows Credential Locker, Linux Secret Service)

## Quick Install

### Claude Code

```bash
/plugin marketplace add sanjay3290/atlassian-skill
```

### Gemini CLI / Cursor / Codex / Goose

```bash
npx skills add sanjay3290/atlassian-skill
```

## Setup

### Install Dependencies

```bash
pip install -r requirements.txt
```

### Option 1: OAuth 2.1 via MCP Server (Recommended)

No API tokens or instance URLs needed. Uses dynamic client registration and PKCE.

```bash
python scripts/auth.py login --oauth
```

A browser opens for Atlassian authorization. Select which products (Jira, Confluence, Compass) to grant access. Tokens are stored in the system keyring and auto-refresh when expired.

### Option 2: API Token (Fallback)

For environments where browser-based OAuth is not available.

1. Create an API token at: https://id.atlassian.com/manage-profile/security/api-tokens
2. Run the login command and follow the prompts:

```bash
python scripts/auth.py login
```

### Verify Authentication

```bash
python scripts/auth.py status
```

### Logout

```bash
python scripts/auth.py logout
```

## Usage Examples

### Jira

```bash
# Search for open bugs in a project
python scripts/jira.py search "project = DEV AND status = Open"

# Search your assigned issues, most recently updated first
python scripts/jira.py search "assignee = currentUser() ORDER BY updated DESC" --limit 10

# Get full details of an issue
python scripts/jira.py get DEV-123

# Create a bug
python scripts/jira.py create --project DEV --summary "Fix login bug" --type Bug

# Create a story with all fields
python scripts/jira.py create --project DEV --summary "New feature" --type Story \
  --description "Details here" --priority High --assignee "user@example.com" --labels "backend,urgent"

# Transition an issue to a new status
python scripts/jira.py transition DEV-123 "In Progress"

# Add a comment
python scripts/jira.py comment DEV-123 --add "Deployed to staging"

# List all projects
python scripts/jira.py list-projects
```

### Confluence

```bash
# Search pages by keyword
python scripts/confluence.py search "deployment guide"

# Search with CQL
python scripts/confluence.py search "type=page AND space=DEV AND text~\"deployment\""

# Read a page
python scripts/confluence.py read <page-id>

# List all spaces
python scripts/confluence.py list-spaces

# Create a page in a space
python scripts/confluence.py create --title "New Page" --space-id <space-id>

# Create a page with content under a parent page
python scripts/confluence.py create --title "Guide" --space-id <id> --body "<p>Content here</p>" --parent-id <parent-id>

# Update a page
python scripts/confluence.py update <page-id> --title "Updated Title" --body "<p>New content</p>"

# Get child pages
python scripts/confluence.py get-children <page-id>
```

## All Commands

### Jira

| Command | Description | Required Args |
|---------|-------------|---------------|
| `search` | Search issues with JQL | `jql` |
| `get` | Get issue details | `issue_key` |
| `create` | Create new issue | `--project`, `--summary`, `--type` |
| `update` | Update existing issue | `issue_key` |
| `transition` | Change issue status | `issue_key`, `status` |
| `comment` | Add or list comments | `issue_key` |
| `list-projects` | List accessible projects | - |
| `list-statuses` | List statuses for project | `project_key` |
| `auth-info` | Test API connection | - |
| `list-tools` | List MCP tools (OAuth only) | - |

### Confluence

| Command | Description | Required Args |
|---------|-------------|---------------|
| `search` | Search using CQL | `query` |
| `read` | Get page content | `page_id` |
| `list-spaces` | List all spaces | - |
| `get-space` | Get space details | `space_id` |
| `list-pages` | List pages in a space | `--space-id` |
| `create` | Create new page | `--title`, `--space-id` |
| `update` | Update existing page | `page_id` |
| `get-children` | Get child pages | `page_id` |
| `auth-info` | Test API connection | - |
| `list-tools` | List MCP tools (OAuth only) | - |

### JSON Output

Add `--json` flag to any command for machine-readable output.

## Part of AI Skills Collection

This skill is also available in the [ai-skills](https://github.com/sanjay3290/ai-skills) collection with 20+ other skills for databases, image generation, research, Google Workspace, Azure DevOps, and more.

## License

Apache-2.0
