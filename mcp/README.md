# Model Context Protocol (MCP) Configuration

This directory contains the MCP server configurations for the AI-Context repository system.

## Overview

Model Context Protocol (MCP) is a standard for connecting AI tools to external data sources and services. This repository uses two MCP servers to provide comprehensive development support:

1. **AI-Context MCP Server** - Access to coding standards and guidelines
2. **GitHub MCP Server** - General GitHub platform operations

## MCP Servers

### AI-Context MCP Server
- **Purpose**: Provides access to the AI-Context repository for coding standards, guidelines, and development patterns
- **Repository**: `tharpep/ai-context`
- **Capabilities**: 
  - Access to comprehensive coding standards and behavioral guidelines
  - Language-specific coding conventions (C#, JavaScript, etc.)
  - Architectural patterns and principles
  - Project-specific contexts and overrides
  - Code templates and snippets
  - Development tool configurations

### GitHub MCP Server
- **Purpose**: Provides direct GitHub repository operations and management capabilities
- **Repository**: General GitHub platform access
- **Capabilities**:
  - Repository creation, forking, and management
  - File operations (create, read, update, delete files)
  - Issue and pull request management
  - Code search across GitHub repositories
  - User and repository search
  - Branch and commit operations
  - Pull request reviews and merging

## Configuration

### MCP Template (`mcp-template.json`)
The `mcp-template.json` file contains the configuration template for both MCP servers:

```json
{
  "mcpServers": {
    "ai-context": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-github"
      ],
      "env": {
        "GITHUB_REPOSITORY": "tharpep/ai-context"
      }
    },
    "github": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-github"
      ]
    }
  }
}
```

### Server Details

#### AI-Context MCP Server
- **Command**: `npx -y @modelcontextprotocol/server-github`
- **Environment**: `GITHUB_REPOSITORY=tharpep/ai-context`
- **Scope**: Limited to the AI-Context repository for standards and guidelines

#### GitHub MCP Server
- **Command**: `npx -y @modelcontextprotocol/server-github`
- **Environment**: None (general GitHub access)
- **Scope**: Full GitHub platform access for repository operations

## Integration

Both MCP servers work together to provide:
1. **AI-Context MCP**: Standards and guidelines for code quality and consistency
2. **GitHub MCP**: Platform operations for repository management and collaboration

The AI-Context MCP ensures all code generated through GitHub operations adheres to established standards, while the GitHub MCP provides the operational capabilities to manage repositories, issues, and pull requests.

## Usage

### For AI Agents
- Reference the AI-Context MCP for coding standards and guidelines
- Use the GitHub MCP for repository operations and management
- Both servers work together to ensure code quality and consistency

### For Developers
- The MCP servers are automatically configured when using compatible IDEs
- Standards from the AI-Context MCP are applied to all code generation
- GitHub operations use the GitHub MCP for platform integration

## Requirements

- Node.js and npm installed
- `@modelcontextprotocol/server-github` package (installed automatically via npx)
- GitHub access permissions for the target repositories

## References

- [Model Context Protocol Documentation](https://modelcontextprotocol.io/)
- [GitHub MCP Server](https://github.com/modelcontextprotocol/servers/tree/main/src/github)
- [AI-Context Repository](https://github.com/tharpep/ai-context)
