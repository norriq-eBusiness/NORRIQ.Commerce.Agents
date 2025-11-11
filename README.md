# NORRIQ Commerce Agents

Claude Code plugin containing specialized agents for the NORRIQ Commerce department. These agents help with implementation planning, code quality validation, thorough code reviews, and QA testing.

## Prerequisites

### Azure CLI Setup (Required for Scout & Chaos Monkey)

Scout and Chaos Monkey agents fetch work items from Azure DevOps and require Azure CLI with the DevOps extension.

**1. Install Azure CLI:**

- **macOS:** `brew install azure-cli`
- **Windows:** Download from https://aka.ms/installazurecliwindows
- **Linux:** https://learn.microsoft.com/en-us/cli/azure/install-azure-cli-linux

**2. Install Azure DevOps Extension:**

```bash
az extension add --name azure-devops
```

**3. Authenticate:**

```bash
az login
az devops configure --defaults organization=https://norriq.visualstudio.com
```

**4. Verify Setup:**

```bash
# Test by fetching any work item
az boards work-item show --id <any-work-item-id>
```

If you see work item details, you're all set!

**Note:** Bouncer and Sherlock do not require Azure CLI - they work directly with your codebase.

**Note:** Chaos Monkey replaces the former "Karen" agent (renamed in v0.3.3 for clarity).

## Agents

### 🔍 Scout - Implementation Planning

**Purpose:** Pre-implementation planning agent for speed date meetings.

**When to use:** After user story reaches "Definition of Ready", before implementation starts.

**What it does:**
- Fetches Azure DevOps user story
- Analyzes requirements and suggests approach
- Searches codebase for similar implementations
- Identifies what could break (impact analysis)
- Generates pseudo code / implementation outline
- Suggests testing strategy
- Identifies documentation needs
- Reviews and adjusts estimate
- Creates comprehensive speed date report covering all 7 talking points

**Talking Points Covered:**
1. Hvordan forventer jeg at løse opgaven?
2. Er det noget vi har lavet før på andre løsninger?
3. Hvad kan jeg potentielt komme til at slå i stykker?
4. Hvordan testes opgaven efterfølgende?
5. Hvad skal dokumenteres (internt/eksternt)?
6. Pseudo kode
7. Review af estimat

**Usage:**
```bash
# In Claude Code CLI
/agent scout

# Provide Azure DevOps user story URL when prompted
# Example: https://norriq.visualstudio.com/Team%20Ecommerce/_workitems/edit/12345
```

### 🚪 Bouncer - Pre-PR Validation

**Purpose:** Fast validation agent that catches critical issues before PR submission.

**When to use:** Run before creating a PR (required by team policy).

**What it does:**
- Runs compilation checks (dotnet build / npm run lint)
- Scans for critical breaking patterns:
  - SSR/hydration issues (unguarded browser APIs)
  - Async/await deadlocks (.Result, .Wait())
  - Hardcoded secrets and API URLs
  - Manual edits to auto-generated files
- Fast execution (<1 minute)
- Pass/fail criteria for PR submission

**Technologies:**
- **Frontend:** Vue 3+, Nuxt 3+, TypeScript
- **Backend:** .NET 8, MediatR, Clean Architecture

**Usage:**
```bash
# In Claude Code CLI
/agent bouncer
```

### 🔍 Sherlock - Code Review Assistant

**Purpose:** Thorough code review agent that helps reviewers provide comprehensive feedback.

**When to use:** During PR review process (for reviewers).

**What it does:**
- Deep analysis of code changes
- Architecture and pattern validation:
  - Clean Architecture adherence
  - CQRS/MediatR patterns
  - Fat handler detection
  - State management review
- Security audit
- Performance patterns
- Code quality and maintainability
- Educational feedback with examples
- Balanced review (highlights good code too)

**Usage:**
```bash
# In Claude Code CLI
/agent sherlock
```

### 🐵 Chaos Monkey - Adversarial QA Validator

**Purpose:** Adversarial QA agent inspired by Netflix's chaos engineering. Validates features match requirements and systematically tries to break them.

**When to use:** After implementation, before marking work item as done.

**What it does:**
- Fetches Azure DevOps work item (user story/bug)
- Validates implementation against acceptance criteria
- Checks for regressions in related code
- Performs chaos testing (adversarial testing):
  - Invalid inputs and boundary conditions
  - Unexpected user flows
  - Missing validation and error handling
  - Race conditions and concurrent usage
  - Security vulnerabilities (XSS, injection, auth bypass)
  - Tries to break the feature in creative ways
- Generates comprehensive QA report:
  - ✅ Acceptance criteria validation
  - 🔥 Chaos testing results (critical issues)
  - ⚠️ Warnings and edge cases
  - 🔒 Security concerns
  - 📋 Suggested tests

**Usage:**
```bash
# In Claude Code CLI
/agent chaos-monkey

# Provide Azure DevOps work item URL when prompted
# Example: https://norriq.visualstudio.com/Team%20Ecommerce/_workitems/edit/12345
```

## Installation

### Via Marketplace (Recommended)

**Step 1: Add the NORRIQ Claude Marketplace**

```bash
/plugin marketplace add norriq-eBusiness/NORRIQ.Claude.Marketplace
```

**Step 2: Install the Plugin**

```bash
/plugin install norriq-commerce-agents@norriq-claude-marketplace
```

**Step 3: Verify Installation**

```bash
/plugin list
```

You should see `norriq-commerce-agents` in the list.

### Getting Updates

When new versions are released:

```bash
/plugin uninstall norriq-commerce-agents
/plugin install norriq-commerce-agents@norriq-claude-marketplace
```

### Advanced: Team Deployment (Optional)

For automatic installation across projects, add to your project's `.claude/settings.json`:

```json
{
  "plugins": [
    {
      "name": "norriq-commerce-agents",
      "source": "norriq-eBusiness/NORRIQ.Commerce.Agents"
    }
  ]
}
```

Team members will automatically get the plugin when they trust the folder.

## Workflow

### For Developers

**Before submitting a PR:**

1. Complete your code changes
2. Run Bouncer: `/agent bouncer`
3. Fix any CRITICAL issues
4. Address RECOMMENDATIONS (optional but encouraged)
5. Create PR once Bouncer passes

### For Reviewers

**During PR review:**

1. Open the PR branch
2. Run Sherlock: `/agent sherlock`
3. Use Sherlock's feedback to supplement your review
4. Focus on issues Sherlock highlights
5. Provide feedback to developer

## Report Structure

### Bouncer Reports

**🚨 CRITICAL ISSUES** - Must fix before PR
- Compilation errors
- Breaking patterns
- Security vulnerabilities

**⚠️ RECOMMENDATIONS** - Consider improving
- Code quality suggestions
- Best practice tips

**✅ Summary**
- Build: Pass/Fail
- Lint: Pass/Fail
- Overall: Pass/Fail

### Sherlock Reports

**🚨 CRITICAL ISSUES** - Production risks

**⚠️ WARNINGS** - Maintainability concerns

**💡 SUGGESTIONS** - Optimization opportunities

**✅ POSITIVE OBSERVATIONS** - Good practices highlighted

**📊 Summary** - Overall assessment

## Supported Technologies

### Frontend
- **Frameworks:** Vue 3+, Nuxt 3+, Nuxt 4+
- **Language:** TypeScript
- **State:** Pinia
- **Styling:** Tailwind CSS + Nuxt UI
- **Auth:** Auth.js
- **API:** Auto-generated client (NSwag)

### Backend
- **Framework:** .NET 8
- **Architecture:** Clean Architecture
- **Patterns:** CQRS with MediatR
- **ORM:** ElasticSearch (NoSQL)
- **Auth:** JWT
- **Logging:** NLog
- **Monitoring:** Elastic APM
- **Secrets:** Azure Key Vault

## Version Handling

Both agents automatically detect framework versions:
- **Minimum required:** Vue 3+, Nuxt 3+
- **Legacy support:** Gracefully handles older versions by skipping modern-only checks
- **Future-proof:** Works with Nuxt 4+ and future versions

## Contributing

### Adding New Checks

1. Update the appropriate agent markdown file
2. Test with sample projects
3. Document the check clearly
4. Commit and push changes

### Agent Structure

```
agents/
├── bouncer.md      # Pre-PR validation agent
└── sherlock.md     # Code review agent
```

Each agent is a markdown file with:
- Frontmatter (name, description, tools, model, color)
- Detailed instructions
- Check patterns
- Report format

## Support

For issues or feature requests, contact the NORRIQ Commerce team or create an issue in the repository.

## License

Internal use only - NORRIQ Commerce Department
