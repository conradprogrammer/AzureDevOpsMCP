# AzureDevOpsMCP
On-Prem Azure DevOps with your own MCP Server directly accessible by AI LLM such as Claude Code extension in VS Code Editor
<p align="center">
  <img src="mcp001.jpg" alt="App screenshot 3" width="800" />
</p>
<p align="center">
  <img src="devops1.jpg" alt="App screenshot 1" width="800" />
</p>
<p align="center">
  <img src="devops2.jpg" alt="App screenshot 2" width="800" />
</p>

# Azure DevOps MCP Server

Model Context Protocol server for Azure DevOps, enabling Claude Code to interact with work items, Git repositories, and pipelines.

## Features

### Work Item Management
- Get, query, create, update work items
- Manage Epics, Features, User Stories, Tasks, Bugs
- Link and organize work items
- Track progress and assignments

### Git Integration
- Link commits to work items
- View commit history
- Create and manage pull requests
- Check PR status and reviews

### Pipeline Automation
- Trigger builds
- Monitor pipeline status
- Retrieve build logs

## Quick Start

### Prerequisites
- .NET 10 Runtime
- Azure DevOps Server (on-premises) or Azure DevOps Services
- Claude Desktop with MCP support

### Installation

1. **Clone and build:**
   ```bash
   git clone <repository-url>
   cd AzureDevOpsMCP
   dotnet build
   ```

2. **Configure Azure DevOps connection:**

   Edit `src/AzureDevOpsMcp.Server/appsettings.json`:
   ```json
   {
     "AzureDevOps": {
       "ServerUrl": "http://your-server/DefaultCollection/",
       "ProjectName": "YourProject",
       "UseWindowsAuth": true,
       "TimeoutSeconds": 30
     }
   }
   ```

3. **Configure Claude Desktop:**

   Add to your Claude Desktop MCP configuration:
   ```json
   {
     "mcpServers": {
       "azure-devops": {
         "command": "dotnet",
         "args": ["run", "--project", "path/to/src/AzureDevOpsMcp.Server"]
       }
     }
   }
   ```

4. **Start using with Claude!**

## Available MCP Tools

### Work Items
| Tool | Description |
|------|-------------|
| `devops_get_work_item` | Get work item details by ID |
| `devops_query_work_items` | Execute WIQL query |
| `devops_get_my_work` | Get items assigned to current user |
| `devops_get_next_task` | Get next task to work on |
| `devops_get_epics` | Get all Epics |
| `devops_get_stories` | Get all User Stories |
| `devops_get_tasks` | Get all Tasks |
| `devops_create_work_item` | Create new work item |
| `devops_update_work_item` | Update work item fields |
| `devops_change_state` | Change work item state |
| `devops_link_work_items` | Link two work items |
| `devops_unlink_work_items` | Remove link between items |

### Git
| Tool | Description |
|------|-------------|
| `devops_link_commit_to_work_item` | Link commit to work item |
| `devops_get_commits` | Get commit history |
| `devops_create_pull_request` | Create pull request |
| `devops_get_pull_request_status` | Get PR status and reviews |

### Pipelines
| Tool | Description |
|------|-------------|
| `devops_trigger_pipeline` | Trigger a pipeline build |
| `devops_get_pipeline_status` | Get build status |
| `devops_get_build_logs` | Get build logs |

See [docs/TOOLS.md](docs/TOOLS.md) for complete tool reference with parameters.

## Example Usage with Claude

**Get work item:**
> Show me work item #25

**Create task:**
> Create a task called "Implement login validation" under user story #10

**Query work:**
> What tasks are assigned to me?

**Link commit:**
> Link commit a3f5c2d to work item #25

**Create PR:**
> Create a pull request from feature/auth to main

**Check build:**
> What's the status of build #123?

## Configuration

### appsettings.json
```json
{
  "AzureDevOps": {
    "ServerUrl": "http://192.168.0.57/DefaultCollection/",
    "ProjectName": "Test001",
    "UseWindowsAuth": true,
    "TimeoutSeconds": 30
  }
}
```

### Environment Variables (Override)
```bash
# Override PAT (switches to PAT auth)
$env:DEVOPS_PAT="your-token-here"

# Override server URL
$env:DEVOPS_SERVER_URL="http://different-server/Collection/"

# Override project
$env:DEVOPS_PROJECT="AnotherProject"
```

## Development

### Build
```bash
dotnet build
```

### Run Tests
```bash
dotnet test
```

### Run Locally
```bash
dotnet run --project src/AzureDevOpsMcp.Server
```

## Architecture

- **MCP Protocol**: stdio transport
- **Authentication**: Windows Authentication or PAT
- **Azure DevOps Client**: Official Microsoft libraries
- **Framework**: .NET 10

## Project Structure

```
AzureDevOpsMCP/
├── src/
│   └── AzureDevOpsMcp.Server/
│       ├── Configuration/       # Settings classes
│       ├── Connection/          # Azure DevOps connection
│       ├── Models/              # Data models
│       ├── Tools/               # MCP tool implementations
│       │   ├── WorkItems/       # Work item tools (12 tools)
│       │   ├── Git/             # Git tools (4 tools)
│       │   └── Pipelines/       # Pipeline tools (3 tools)
│       └── Program.cs           # Entry point
├── tests/
│   ├── AzureDevOpsMcp.Tests/           # Unit tests
│   └── AzureDevOpsMcp.IntegrationTests/ # Integration tests
├── deployment/                  # CI/CD configuration
│   └── azure-pipelines.yml
└── docs/
    └── TOOLS.md                 # Tool reference
```

## Troubleshooting

### Connection Failed
- Verify server URL in `appsettings.json`
- Check network access to DevOps server
- Ensure Windows Authentication is configured
- Try accessing DevOps web UI to verify credentials

### Project Not Found
- Verify project name matches exactly (case-sensitive)
- Check you have access to the project
- Verify collection name in URL

### Build Errors
- Ensure .NET 10 SDK installed: `dotnet --version`
- Restore packages: `dotnet restore`
- Clean build: `dotnet clean && dotnet build`

## License

MIT License

## Contributing

Contributions welcome! See [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.
