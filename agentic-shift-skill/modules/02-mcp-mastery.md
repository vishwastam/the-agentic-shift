# Module 2: MCP Mastery - External Tool Integration

## Learning Objectives

By the end of this module, learners will:
- Understand what MCP (Model Context Protocol) servers provide
- Configure at least one MCP server for their workflow
- Successfully query external systems through Claude

## What is MCP?

### Teaching Points

Explain:

1. **MCP = Real-time context** - Claude can access live data from your tools
2. **Not just static docs** - Query GitHub issues, database schemas, Slack messages in real-time
3. **Standardized protocol** - One integration pattern for many services

### Available MCP Servers

| Server | What It Provides | Best For |
|--------|-----------------|----------|
| **GitHub** | Issues, PRs, code search, repo info | Code review, issue triage |
| **Postgres** | Schema, tables, query execution | Database work, migrations |
| **Slack** | Channels, messages, user info | Team communication |
| **Filesystem** | Enhanced file operations | Large codebases |
| **Memory** | Persistent key-value storage | Cross-session context |
| **Puppeteer** | Browser automation | Testing, scraping |
| **Brave Search** | Web search results | Research tasks |

## Lesson 2.1: Assess Integration Needs

### Discovery Questions

Ask the learner:

1. "Where does your team track issues and PRs?"
   - GitHub → Configure GitHub MCP
   - GitLab → Note: GitLab MCP may be community-maintained
   - Jira → Configure Jira MCP

2. "Do you work with databases directly?"
   - PostgreSQL → Configure Postgres MCP
   - MySQL → Configure MySQL MCP
   - MongoDB → Configure MongoDB MCP

3. "Where does your team communicate?"
   - Slack → Configure Slack MCP
   - Discord → Note available options

4. "What would be MOST valuable to access through Claude right now?"

Based on their answer, proceed with the most relevant MCP.

## Lesson 2.2: MCP Configuration

### Configuration File Location

```bash
# Check if MCP config exists
CONFIG_FILE=~/.claude/mcp_servers.json
test -f "$CONFIG_FILE" && echo "Config exists" || echo "Need to create config"
```

### Template Structure

```json
{
  "mcpServers": {
    "server-name": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-name"],
      "env": {
        "REQUIRED_VAR": "value"
      }
    }
  }
}
```

## Interactive Exercise 2.1: Configure GitHub MCP

### Prerequisites

1. User needs a GitHub Personal Access Token
2. Token needs appropriate scopes (repo, read:org for org repos)

### Steps

1. **Create token** (if they don't have one):
   ```
   Go to: GitHub → Settings → Developer Settings → Personal Access Tokens → Tokens (classic)
   Create with scopes: repo, read:org, read:user
   ```

2. **Set environment variable**:
   ```bash
   # Add to shell profile (~/.zshrc, ~/.bashrc, etc.)
   export GITHUB_TOKEN="ghp_xxxxxxxxxxxxxxxxxxxx"

   # Reload
   source ~/.zshrc  # or restart terminal
   ```

3. **Create/update MCP config**:
   ```json
   {
     "mcpServers": {
       "github": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-github"],
         "env": {
           "GITHUB_TOKEN": "${GITHUB_TOKEN}"
         }
       }
     }
   }
   ```

4. **Write the config file**:
   Create `~/.claude/mcp_servers.json` with the above content.

5. **Restart Claude Code** to pick up the new MCP.

### Verification

Test GitHub MCP with these queries:

```
"List open issues in this repository"
"Show me the most recent 5 pull requests"
"Search for files containing 'TODO' in this repo"
"What branches exist and which is default?"
```

## Interactive Exercise 2.2: Configure Postgres MCP (Alternative)

### Prerequisites

1. User needs database connection string
2. Database should be accessible (local or remote with proper credentials)

### Steps

1. **Gather connection info**:
   ```
   Host: localhost (or remote host)
   Port: 5432
   Database: mydb
   User: myuser
   Password: mypassword
   ```

2. **Set environment variable**:
   ```bash
   export DATABASE_URL="postgresql://user:password@host:5432/dbname"
   ```

3. **Add to MCP config**:
   ```json
   {
     "mcpServers": {
       "postgres": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-postgres"],
         "env": {
           "DATABASE_URL": "${DATABASE_URL}"
         }
       }
     }
   }
   ```

### Verification

Test Postgres MCP:

```
"What tables exist in the database?"
"Describe the schema of the users table"
"How many records are in each table?"
"Write a query to find users created in the last 7 days"
```

## Interactive Exercise 2.3: Configure Slack MCP (Alternative)

### Prerequisites

1. Slack workspace admin access or ability to create apps
2. Bot token with appropriate scopes

### Steps

1. **Create Slack App**:
   ```
   Go to: api.slack.com/apps → Create New App
   Choose: From scratch
   Add scopes: channels:read, chat:write, users:read
   Install to workspace
   Copy Bot User OAuth Token
   ```

2. **Set environment variable**:
   ```bash
   export SLACK_BOT_TOKEN="xoxb-xxxxxxxxxxxx"
   ```

3. **Add to MCP config**:
   ```json
   {
     "mcpServers": {
       "slack": {
         "command": "npx",
         "args": ["-y", "@modelcontextprotocol/server-slack"],
         "env": {
           "SLACK_BOT_TOKEN": "${SLACK_BOT_TOKEN}"
         }
       }
     }
   }
   ```

### Verification

Test Slack MCP:

```
"List channels I have access to"
"What are the most recent messages in #general?"
"Send a test message to #test-channel: 'Hello from Claude!'"
```

## Lesson 2.3: MCP Best Practices

### Security Considerations

Teach:

1. **Token scoping**: Use minimum required permissions
2. **Environment variables**: Never hardcode tokens in config
3. **Read-only first**: Start with read access, add write only if needed
4. **Audit access**: Review what Claude accesses through MCPs

### Workflow Integration

Example workflows with MCP:

**Morning standup with GitHub**:
```
"Summarize my PRs from yesterday, any reviews I need to do,
and issues assigned to me"
```

**Database-aware development**:
```
"Based on our database schema, write a function that fetches
a user with their orders from the last 30 days"
```

**Team sync with Slack**:
```
"Check #incidents for any active issues, summarize for me"
```

## Troubleshooting

### Common Issues

| Issue | Solution |
|-------|----------|
| "MCP server not found" | Ensure `npx` is in PATH, try `npm install -g @modelcontextprotocol/server-name` |
| "Authentication failed" | Verify token is set and has correct scopes |
| "Connection refused" | Check network access, firewall rules, VPN requirements |
| "Timeout" | Server may be slow; check if service is accessible manually |

### Debug Steps

```bash
# Test if npx can find the server
npx -y @modelcontextprotocol/server-github --help

# Check environment variable
echo $GITHUB_TOKEN

# Test connection manually (for databases)
psql $DATABASE_URL -c "SELECT 1"
```

## Checkpoint

Module 2 is complete when:
- [ ] At least one MCP server is configured in `~/.claude/mcp_servers.json`
- [ ] User successfully queried external data through Claude
- [ ] User understands security implications of MCP access
- [ ] User can explain how MCP improves their workflow

## Transition

---

**Module 2 Complete!**

You've connected Claude to your real tools. Now Claude can access live data from your GitHub, database, or team communication platform.

This context will be invaluable for the next module, where you'll learn the core workflow pattern: **Test-Driven Agentic Development**.

Type "next" or run `/onboard-tda` to continue.

---
