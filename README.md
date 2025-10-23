# AI Context Repository

A comprehensive context system for AI-powered IDE assistants to ensure consistent, high-quality code across all development projects.

## 🎯 For AI Agents

**Primary Reference**: See `.cursor/rules/` folder for:
- **`comprehensive.mdc`** - Core AI agent standards and behavioral guidelines
- **`ai-context-usage.md`** - How to use and update this repository

## 📁 Repository Structure

```
ai-context/
├── .cursor/rules/                # 🎯 START HERE - Core AI guidelines
├── languages/                    # Language-specific standards
├── architecture/                 # Architectural patterns and principles
├── projects/                     # Active project contexts
├── tools/                        # Development tools and configurations
├── templates/                    # Code templates and snippets
└── mcp/                         # Model Context Protocol configurations
```

## 🚀 Quick Start

1. **AI Agents**: Reference `.cursor/rules/comprehensive.mdc` for core standards
2. **Developers**: See `.cursor/rules/ai-context-usage.md` for detailed usage guidelines
3. **Language Standards**: Check `languages/` folder for specific coding conventions
4. **Architecture**: Use `architecture/` folder for design patterns and principles

## 📖 Documentation

- **Core Standards**: `.cursor/rules/comprehensive.mdc`
- **Usage Guide**: `.cursor/rules/ai-context-usage.md`
- **Language Standards**: `languages/` folder
- **Architecture Patterns**: `architecture/` folder
- **Project Contexts**: `projects/` folder
- **MCP Configuration**: `mcp/` folder

## 🔌 Model Context Protocol (MCP) Integration

This repository includes MCP server configurations for enhanced AI assistant capabilities:

### AI-Context MCP Server
- **Purpose**: Provides access to coding standards, guidelines, and development patterns
- **Repository**: `tharpep/ai-context`
- **Capabilities**: Standards enforcement, language conventions, architectural patterns

### GitHub MCP Server
- **Purpose**: Direct GitHub repository operations and management
- **Capabilities**: Repository management, issue tracking, pull request operations

### Configuration
Both MCP servers are configured via `mcp/mcp-template.json` and work together to ensure:
- Code quality through standards enforcement
- Seamless GitHub platform integration
- Consistent development workflows across all projects

See `mcp/README.md` for detailed MCP configuration and usage instructions.

---

*This repository provides comprehensive coding standards and AI-assisted development workflows with integrated MCP server support.*