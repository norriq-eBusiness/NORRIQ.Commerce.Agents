# CLAUDE.md

This file provides guidance to Claude Code when working with this plugin repository.

## Project Overview

This is a Claude Code plugin repository containing specialized agents for the NORRIQ Commerce department. The plugin provides two agents that help maintain code quality through automated validation and review.

## Repository Structure

```
NORRIQ.Commerce.Agents/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── agents/
│   ├── bouncer.md           # Pre-PR validation agent
│   └── sherlock.md          # Code review agent
├── commands/                 # (Reserved for future slash commands)
├── CLAUDE.md                # This file
└── README.md                # User documentation
```

## Plugin Architecture

### Plugin Manifest (.claude-plugin/plugin.json)

- **name:** `norriq-commerce-agents` (kebab-case required)
- **version:** Semantic versioning
- **description:** Short plugin description
- **agents:** Points to `agents/` directory
- **commands:** Points to `commands/` directory (future use)

### Agent Files

Each agent is a markdown file with frontmatter and instructions:

**Frontmatter format:**
```yaml
---
name: agent-name           # Kebab-case, no spaces
description: "..."         # Clear, concise description
tools: Tool1, Tool2        # Available tools for agent
model: sonnet              # Model to use
color: blue                # UI color
---
```

**Agent instructions:**
- Clear workflow steps
- Technology-specific checks
- Pattern scanning guidance
- Report format specifications

## Agents

### Bouncer (agents/bouncer.md)

**Purpose:** Fast pre-PR validation (<1 minute)

**Key responsibilities:**
1. Auto-detect project type (frontend/backend/both)
2. Detect framework versions (Vue/Nuxt/React)
3. Run compilation/linting
4. Scan for critical patterns:
   - SSR hydration issues
   - Async deadlocks
   - Hardcoded secrets
   - Auto-generated file edits
5. Report CRITICAL vs RECOMMENDATIONS

**Tools:** Bash, Glob, Grep, Read

**Critical patterns:**
- Frontend: Unguarded `window`/`document`/`localStorage`, hardcoded URLs, non-deterministic SSR values
- Backend: `.Result`/`.Wait()` on async, hardcoded connection strings, missing async on I/O

### Sherlock (agents/sherlock.md)

**Purpose:** Thorough code review assistance

**Key responsibilities:**
1. Analyze git diff and changed files
2. Deep architecture review
3. Security audit
4. Performance pattern analysis
5. Educational feedback with examples
6. Highlight positive practices

**Tools:** Bash, Glob, Grep, Read

**Review areas:**
- Frontend: Component structure, composables, state management, SSR/client handling, API integration
- Backend: Clean Architecture, CQRS/MediatR, async patterns, fat handlers, DI, security

## Technology Context

### Frontend Stack (NORRIQ Commerce)

**Baseline project:** `/Users/opc/Documents/git/Accelerator/Accelerator.Frontend`

**Stack:**
- Nuxt 3+ with Vue 3+ (Composition API)
- TypeScript
- Pinia (state management)
- Tailwind CSS + Nuxt UI
- Auth.js for authentication
- Auto-generated API client (NSwag from Swagger)
- SSR with hydration considerations

**Common issues to catch:**
- Unguarded browser APIs causing hydration mismatches
- Manual edits to `api/swaggerApiClient.ts`
- Hardcoded API URLs (should use runtime config)
- Missing ClientOnly wrappers
- Non-deterministic template values

### Backend Stack (NORRIQ Commerce)

**Baseline project:** `/Users/opc/Documents/git/Accelerator/Accelerator.Api`

**Stack:**
- .NET 8
- Clean Architecture (Domain, Application, Infrastructure, Web.Api)
- CQRS with MediatR
- ElasticSearch (NoSQL via NORRIQ packages)
- JWT authentication
- NLog logging
- Azure Key Vault for secrets
- Elastic APM monitoring
- Business Central integration

**Common issues to catch:**
- `.Result` or `.Wait()` on async (deadlocks)
- Fat handlers (>100 lines, complex logic that belongs in services)
- Hardcoded secrets/connection strings
- Missing async/await on I/O operations
- Business logic in controllers
- Missing CancellationToken parameters

## Development Guidelines

### Adding New Checks

1. **Identify the issue pattern** - What code causes problems?
2. **Determine severity** - CRITICAL (breaks) or RECOMMENDATION (best practice)?
3. **Choose the right agent:**
   - Bouncer: Only critical breaking issues
   - Sherlock: All quality/architecture concerns
4. **Add grep patterns** or read instructions
5. **Document why it matters** and how to fix
6. **Test with real repos**

### Modifying Agents

**When updating agent instructions:**

1. Read the current agent file
2. Make focused changes to specific sections
3. Preserve the overall structure and format
4. Test the agent works as expected
5. Update README.md if user-facing behavior changes
6. Commit with clear description

**Don't:**
- Change the agent name (breaks installations)
- Remove entire sections without reason
- Add checks that significantly slow execution (Bouncer must be fast)
- Make assumptions about tech stack without verification

### Version Compatibility

Both agents check `package.json` for framework versions:
- **Minimum supported:** Vue 3+, Nuxt 3+
- **Legacy handling:** Skip modern-only checks for Vue 2, Nuxt 2
- **Future-proof:** Works with Nuxt 4+ without changes

When adding version-specific checks, always:
1. Detect version from package.json
2. Default to modern stack
3. Gracefully skip for older versions
4. Document version requirements

## Testing

### Manual Testing

Test agents against real NORRIQ projects:

**Frontend testing:**
```bash
cd /Users/opc/Documents/git/Accelerator/Accelerator.Frontend
# Make test changes that should be caught
/agent bouncer
/agent sherlock
```

**Backend testing:**
```bash
cd /Users/opc/Documents/git/Accelerator/Accelerator.Api
# Make test changes that should be caught
/agent bouncer
/agent sherlock
```

### Test Cases

**Bouncer should catch:**
- Compilation errors
- Unguarded `window.localStorage` in Vue components
- `.Result` on Task in C# files
- Hardcoded API keys
- Modified `api/swaggerApiClient.ts`

**Sherlock should provide:**
- Detailed architecture feedback
- Fat handler detection (>100 lines)
- Security best practices
- Performance recommendations
- Positive observations

## Git Workflow

This repository uses Azure DevOps:
- **Remote:** `https://norriq.visualstudio.com/Team%20Ecommerce/_git/NORRIQ.Commerce.Agents`
- **Branch strategy:** Main branch for stable releases
- **Commits:** Clear, descriptive commit messages

## Plugin Distribution

### Team Usage

Team members install via:
1. Project `.claude/settings.json` configuration
2. Marketplace installation
3. Direct plugin installation

### Updates

When agents are updated:
1. Version bump in `plugin.json`
2. Clear commit message explaining changes
3. Push to remote
4. Team gets updates automatically or on next install

## Notes

- **Plugin name must be kebab-case** - Directory can be `NORRIQ.Commerce.Agents` but plugin name in manifest must be `norriq-commerce-agents`
- **Bouncer must be fast** - Target <1 minute execution time
- **Sherlock can be thorough** - Taking time is acceptable for quality review
- **No tests currently** - Commerce department doesn't maintain test suites (by policy)
- **Callsigns** - Agents have fun names (Bouncer, Sherlock) to make them more approachable for the team

## Common Tasks

### Update Bouncer checks
1. Read `agents/bouncer.md`
2. Add new pattern to appropriate section (Frontend/Backend)
3. Categorize as CRITICAL or RECOMMENDATION
4. Test with sample code
5. Commit

### Update Sherlock reviews
1. Read `agents/sherlock.md`
2. Add to appropriate review category
3. Provide clear examples
4. Test with real PR
5. Commit

### Add new agent
1. Create `agents/new-agent-name.md`
2. Follow frontmatter format
3. Write clear instructions
4. Update README.md with agent description
5. Test thoroughly
6. Commit

## Reference Links

- Claude Code Plugin Docs: https://code.claude.com/docs/en/plugins
- Plugin Reference: https://code.claude.com/docs/en/plugins-reference
- Accelerator Frontend: `/Users/opc/Documents/git/Accelerator/Accelerator.Frontend`
- Accelerator API: `/Users/opc/Documents/git/Accelerator/Accelerator.Api`
