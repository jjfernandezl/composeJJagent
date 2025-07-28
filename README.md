# composeJJagent

# Simple ADK Repository Health Monitor

A minimal Agent Development Kit (ADK) project that monitors GitHub repository health using local LLM and MCP servers.

## 🎯 What It Does

- **Repository Health Checks**: Verifies README existence and recent commit activity
- **AI-Powered Summaries**: Uses local LLM to generate health insights
- **MCP Integration**: Connects to GitHub and Docker MCP servers via gateway
- **Automated Reports**: Saves structured JSON health reports
- **Fully Containerized**: Everything runs with Docker Compose

## 🏗️ Architecture

```
┌─────────────────┐    ┌─────────────────┐    ┌─────────────────┐
│   Simple Agent  │────│   MCP Gateway   │────│  GitHub/Docker  │
│    (Python)     │    │   (Orchestrator)│    │   MCP Servers   │
└─────────────────┘    └─────────────────┘    └─────────────────┘
         │                       │
         └───────────────────────┼─────────────────────────────────┐
                                 │                                 │
                    ┌─────────────────┐                ┌─────────────────┐
                    │   Local LLM     │                │   JSON Reports  │
                    │   (Ollama)      │                │   (Output Dir)  │
                    └─────────────────┘                └─────────────────┘
```

## 📋 Prerequisites

- Docker Desktop installed and running
- Visual Studio Code
- Git/GitHub account
- GitHub and Docker MCP servers running locally

## 🚀 Quick Start

### 1. Clone and Setup

```bash
git clone <your-repo-url>
cd simple-adk-agent
mkdir agent mcps output
```

### 2. Configure MCP Servers

Update `mcps/github.json` with your GitHub MCP server details:

```json
{
  "name": "github",
  "type": "stdio",
  "command": "your-github-mcp-command-here",
  "args": [],
  "env": {
    "GITHUB_TOKEN": "your-github-token"
  }
}
```

Update `mcps/docker.json` with your Docker MCP server details:

```json
{
  "name": "docker",
  "type": "stdio", 
  "command": "your-docker-mcp-command-here",
  "args": []
}
```

### 3. Update Target Repository

Edit `agent/main.py` and replace the example repository:

```python
test_repos = [
    "your-username/your-repo-name"  # Replace with actual repo!
]
```

### 4. Start Services

```bash
# Start all services
docker-compose up --build -d

# Pull the LLM model (first time only)
docker exec simple-llm ollama pull llama3.2:1b
```

### 5. Run the Agent

```bash
# Execute the health monitor
docker exec -it simple-agent python main.py
```

### 6. Check Results

Health reports will be saved in the `output/` directory as JSON files.

## 📁 Project Structure

```
simple-adk-agent/
├── docker-compose.yml          # Service orchestration
├── agent/
│   ├── Dockerfile             # Agent container setup
│   ├── requirements.txt       # Python dependencies
│   ├── config.yml            # Agent configuration
│   └── main.py               # Main agent logic
├── mcps/
│   ├── github.json           # GitHub MCP config
│   └── docker.json           # Docker MCP config
├── output/                   # Generated health reports
└── README.md                # This file
```

## 🔧 Configuration

The agent is configured via `agent/config.yml`:

```yaml
agent:
  name: "repo-health-monitor"
  description: "Simple repository health checker"

llm:
  base_url: "http://llm:11434"
  model: "llama3.2:1b"

mcp:
  gateway_url: "http://mcp-gateway:8080"
  servers:
    - github
    - docker

health_checks:
  - readme_exists
  - has_license
  - recent_commits
```

## 📊 Sample Output

```json
{
  "repo": "username/repository",
  "timestamp": "2025-07-28T10:30:00",
  "checks": {
    "readme_exists": true,
    "recent_commits": true
  },
  "ai_summary": "Repository appears healthy with documentation and active development. Recent commits indicate ongoing maintenance."
}
```

## 🛠️ Services

| Service | Port | Purpose |
|---------|------|---------|
| **Ollama LLM** | 11434 | Local language model for AI summaries |
| **MCP Gateway** | 8080 | Orchestrates MCP server connections |
| **Simple Agent** | - | Executes health checks and generates reports |

## 🔄 Development Workflow

1. **Modify agent logic** in `agent/main.py`
2. **Restart agent container**: `docker-compose restart agent`
3. **Run health checks**: `docker exec -it simple-agent python main.py`
4. **Check output** in `output/` directory

## 📈 Next Steps

- [ ] Add more health checks (license files, CI status, security alerts)
- [ ] Process multiple repositories automatically
- [ ] Integrate Docker-based code analysis tools
- [ ] Create web dashboard for visualizing results
- [ ] Add scheduling for periodic health monitoring
- [ ] Implement notification system for health degradation

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit changes: `git commit -m 'Add amazing feature'`
4. Push to branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🆘 Troubleshooting

**Agent won't start?**
- Check Docker Desktop is running
- Verify MCP server configurations
- Ensure GitHub token has proper permissions

**LLM responses are slow?**
- The `llama3.2:1b` model is optimized for speed
- Consider upgrading to a larger model for better quality

**MCP connection errors?**
- Verify your MCP servers are running locally
- Check network connectivity between containers
- Review MCP server logs for authentication issues

## 📚 Learn More

- [Docker ADK Documentation](https://docs.docker.com/guides/agentic-ai/)
- [MCP Gateway Repository](https://github.com/docker/mcp-gateway)
- [Anthropic MCP Documentation](https://modelcontextprotocol.io/)

---

**Built with ❤️ using Docker ADK, MCP, and Ollama**
