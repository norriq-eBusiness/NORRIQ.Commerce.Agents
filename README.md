# NORRIQ Commerce Agents

Claude Code plugin containing specialized agents for the NORRIQ Commerce department. These agents help ensure code quality through automated pre-PR validation and thorough code reviews.

## Agents

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
