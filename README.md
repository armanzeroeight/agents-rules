![FastAgent Marketplace](./README.png)

# FastAgent Plugins

**Professional AI Agents & Skills for Claude Code** | Covering Development, DevOps, Cloud, and Beyond

> [!IMPORTANT]
> 🌟 **This project will be part of a bigger ecosystem! STAY TUNED**
> 
> 🥇 Your contributions will be recognized and featured with your name and GitHub profile.
> 
> 🙌 Join our community of developers making AI coding better for everyone!

## What is FastAgent?

FastAgent is a curated collection of professional plugins for Claude Code that extend your AI coding assistant with specialized agents, skills, and commands. Our approach is simple: **pick only what you need, avoid token bloat, and get better results**.

### Why FastAgent?

- **🎯 Focused & Efficient**: Each plugin is laser-focused on specific technologies or roles
- **💰 Token-Conscious**: Only load what you need - save tokens, save costs
- **🔧 Modular Design**: Mix and match plugins based on your workflow
- **📦 Easy Installation**: One command to add capabilities
- **🚀 Production-Ready**: Battle-tested patterns and best practices

## Our Plugin Philosophy

We organize plugins into three tiers to make it easy to choose what you need:

### 🌟 Essential Tier
Broad-spectrum plugins that benefit most developers. These cover fundamental workflows like code review, debugging, and refactoring.

**Who should use**: Everyone

### 👤 Role Tier
Role-based plugins combining multiple technologies and workflows for specific job functions.

**Who should use**: Developers in specialized roles

### 🔧 Tech Tier
Technology-specific plugins for deep expertise in particular tools and frameworks. Choose the ones matching your stack.

**Who should use**: Developers working with specific technologies

## Quick Start

### 1. Install Claude Code

Make sure you have [Claude Code](https://claude.ai/download) installed.

### 2. Add the FastAgent Marketplace

```bash
# In Claude Code, run:
/plugin marketplace add armanzeroeight/agents-skills
```

### 3. Browse and Install Plugins

```bash
# Open the plugin browser
/plugin

# Or install directly
/plugin install <plugin-name>@fastagent-marketplace
```

### 4. Start Using

Once installed, agents and skills activate automatically based on your work context. Commands are available via slash commands.

## Available Plugins

### 🌟 Essential Tier (3 plugins)

| Plugin | Description | Agents | Skills | Commands |
|--------|-------------|--------|--------|----------|
| **testing-toolkit** | Comprehensive testing toolkit for test strategy, coverage analysis, and test data generation | Test Strategist | Test Coverage Analyzer, Test Data Generator | `/run-tests`, `/coverage-report` |
| **git-workflow-toolkit** | Git workflow and branching strategy toolkit with commit message generation and branch management | Git Expert | Commit Message Generator, Branch Strategy Advisor | `/smart-commit`, `/branch-cleanup` |
| **developer-toolkit** | Essential development tools for code review, debugging, and refactoring | Code Reviewer, Debugger, Refactoring Architect | Code Review Practices | `/quick-review`, `/debug-trace` |

---

### 🔧 Tech Tier (8 plugins)

| Plugin | Description | Agents | Skills | Commands |
|--------|-------------|--------|--------|----------|
| **docker-toolkit** | Docker containerization toolkit for image optimization and security scanning | Container Architect | Dockerfile Optimizer, Image Security Scanner | `/optimize-image` |
| **python-toolkit** | Python development toolkit with UV for fast package management and project structure | Python Expert | Python Packaging, Dependency Manager | `/setup-project` |
| **react-toolkit** | React development toolkit for component architecture and state management | React Architect | Component Patterns, State Management Advisor | `/create-component` |
| **api-toolkit** | API design toolkit for REST with endpoint design and OpenAPI documentation | API Architect | REST API Designer, API Documentation Generator | `/generate-openapi` |
| **database-toolkit** | Database design toolkit for schema modeling and query optimization | Database Architect | Schema Designer, Query Optimizer | `/analyze-query` |
| **aws-toolkit** | AWS cloud toolkit for service selection, cost optimization, and security | AWS Architect | AWS Cost Optimizer, Security Group Analyzer | `/estimate-cost` |
| **terraform-toolkit** | Complete Terraform IaC toolkit with module scaffolding and state management | IaC Architect | Module Scaffolder, State Manager, Cost Estimator, Documentation Generator, Dependency Analyzer, Upgrade Assistant | - |
| **kubernetes-toolkit** | Kubernetes container orchestration toolkit for production-ready manifests | K8s Operations Specialist | Kubernetes Best Practices | - |

---

### 👤 Role Tier (4 plugins)

| Plugin | Description | Agents | Skills | Commands |
|--------|-------------|--------|--------|----------|
| **frontend-developer** | Comprehensive frontend toolkit combining React, accessibility, performance, and responsive design | Frontend Lead | Accessibility Checker, Performance Optimizer, Responsive Design Advisor | `/audit-accessibility` |
| **backend-developer** | Comprehensive backend toolkit combining API design, database, security, and scalability | Backend Lead | API Security Checker, Database Migration Helper | `/create-migration` |
| **devops-engineer** | Comprehensive DevOps toolkit combining CI/CD pipelines, infrastructure automation, and monitoring | DevOps Lead | CI/CD Optimizer, Infrastructure Monitor | `/check-pipeline` |
| **data-engineer** | Comprehensive data engineering toolkit combining ETL pipelines, data quality, and data architecture | Data Architect | ETL Designer, Data Quality Checker | `/validate-pipeline` |

## How It Works

### Agents = Strategy
Agents make high-level architectural decisions and choose the right approach. They focus on the "what" and "why".

**Example**: The Code Reviewer agent decides review scope, prioritizes issues, and determines severity levels.

### Skills = Tactics
Skills handle specific operations with focused, step-by-step instructions. They focus on the "how".

**Example**: The Code Review Practices skill provides detailed guidance on conducting effective reviews.

### Commands = Quick Actions
Commands are user-invoked shortcuts for frequently-used prompts.

**Example**: `/quick-review` triggers a fast code review of specific files.

### The Magic: Automatic Delegation

Agents automatically delegate to skills when needed. You get strategic thinking at the top level and tactical execution when required - all without manual intervention.

## Team Configuration (Optional)

For automatic plugin installation across your team, add to your project's `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "fastagent-marketplace": {
      "source": {
        "source": "github",
        "repo": "armanzeroeight/agents-skills"
      }
    }
  },
  "enabledPlugins": [
    "developer-toolkit@fastagent-marketplace",
    "terraform-toolkit@fastagent-marketplace"
  ]
}
```

When team members trust the repository, plugins install automatically.

## Usage Examples

### Using Agents

Agents activate automatically based on context:

```bash
# Code review
> Review my recent changes

# Debugging
> Help me debug this error

# Infrastructure design
> Design a Terraform module for our VPC
```

Or invoke explicitly:

```bash
> Use the code-reviewer agent to check my PR
> Ask the iac-architect agent about module design
```

### Using Skills

Skills are invoked automatically by agents or Claude when relevant:

```bash
# Triggers terraform-module-scaffolder skill
> Create a new Terraform module for S3 buckets

# Triggers code-review-practices skill
> What should I look for in this code review?
```

### Using Commands

Commands are user-invoked with slash syntax:

```bash
# Quick code review
/quick-review

# Debug trace
/debug-trace
```

## Managing Plugins

### View Installed Plugins

```bash
/plugin
```

### Enable/Disable Plugins

```bash
# Disable without uninstalling
/plugin disable <plugin-name>@fastagent-marketplace

# Re-enable
/plugin enable <plugin-name>@fastagent-marketplace
```

### Uninstall Plugins

```bash
/plugin uninstall <plugin-name>@fastagent-marketplace
```

### Update Marketplace

```bash
/plugin marketplace update fastagent-marketplace
```

## Token Efficiency

One of FastAgent's core principles is token efficiency. Here's how we help you save:

### Only Load What You Need

Instead of one massive plugin with everything, choose specific plugins:

```bash
# ❌ Don't load everything
# One giant plugin with all technologies

# ✅ Load only what you use
/plugin install terraform-toolkit@fastagent-marketplace  # Only if you use Terraform
/plugin install kubernetes-toolkit@fastagent-marketplace  # Only if you use K8s
```

### Progressive Disclosure

Skills use progressive disclosure - detailed documentation is loaded only when needed:

```
skill/
├── SKILL.md          # Loaded always (concise)
└── references/       # Loaded only when needed (detailed)
```

### Focused Skills

Each skill does one thing well, keeping context lean:

- ✅ `terraform-state-manager` - State operations only
- ✅ `terraform-cost-estimator` - Cost analysis only
- ❌ `terraform-everything` - Too broad, wastes tokens

## Contributing

We welcome contributions! Here's how:

### Adding a New Plugin

1. Fork the repository
2. Create plugin directory: `plugins/your-plugin-name/`
3. Add agents, skills, and commands
4. Update `.claude-plugin/marketplace.json`
5. Submit a pull request

### Plugin Structure

```
plugins/your-plugin/
├── agents/
│   └── your-agent.md
├── skills/
│   └── your-skill/
│       └── SKILL.md
└── commands/
    └── your-command.md
```

### Guidelines

- **Agents**: Focus on strategy and high-level decisions
- **Skills**: Provide tactical, step-by-step guidance
- **Commands**: Keep simple and focused on one action
- **Documentation**: Clear descriptions with trigger words
- **Testing**: Test with Claude Code before submitting

See [Plugin Design Manifesto](.docs/plugin-design-manifesto.md) for detailed guidelines.

## Documentation

- [Plugin Design Manifesto](.docs/plugin-design-manifesto.md) - Design principles and best practices
- [Claude Plugins](.docs/claude-plugins.md) - Plugin system overview
- [Claude Marketplaces](.docs/claude-marketplaces.md) - Marketplace management
- [Claude Commands](.docs/claude-commands.md) - Slash commands guide
- [Claude Skills](.docs/claude-skills.md) - Agent Skills documentation
- [Claude Subagents](.docs/claude-subagents.md) - Subagents guide

## Support

- **Issues**: [GitHub Issues](https://github.com/armanzeroeight/agents-skills/issues)
- **Discussions**: [GitHub Discussions](https://github.com/armanzeroeight/agents-skills/discussions)
- **Website**: [gofastagent.com](https://gofastagent.com)

## License

MIT License - see [LICENSE](LICENSE) for details.

---

Made with 💜 by the community, for the community.
