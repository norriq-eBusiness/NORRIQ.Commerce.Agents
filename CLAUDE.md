# CLAUDE.md

This file provides guidance to Claude Code when working with this plugin repository.

## Project Overview

This is a Claude Code plugin repository containing specialized agents for the NORRIQ Commerce department. The plugin provides four agents that help with implementation planning, code quality validation, code review, and QA testing.

## Repository Structure

```
NORRIQ.Commerce.Agents/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── agents/
│   ├── scout.md             # Pre-implementation planning agent
│   ├── bouncer.md           # Pre-PR validation agent
│   ├── sherlock.md          # Code review agent
│   └── chaos-monkey.md      # Adversarial QA validation agent
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

### Scout (agents/scout.md)

**Purpose:** Pre-implementation planning for speed date meetings (reconnaissance before coding)

**Key responsibilities:**
1. Fetch Azure DevOps user story
2. Analyze requirements and suggest approach
3. Search codebase for similar implementations
4. Identify impact (what could break)
5. Generate pseudo code/implementation outline
6. Create testing strategy
7. Identify documentation needs
8. Review and adjust estimates
9. Generate comprehensive speed date report

**Tools:** Bash, Glob, Grep, Read

**Workflow covers 7 talking points:**
1. How to solve the task (technical approach)
2. Similar implementations (learning from existing code)
3. What could break (impact analysis)
4. Testing strategy (manual + automated)
5. Documentation needs (internal + external)
6. Pseudo code (commented implementation outline)
7. Estimate review (complexity analysis)

**Output:** Comprehensive planning report for team discussion

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

### Chaos Monkey (agents/chaos-monkey.md)

**Purpose:** Adversarial QA validation and chaos testing (inspired by Netflix's resilience testing)

**Key responsibilities:**
1. Fetch Azure DevOps work item (user story/bug)
2. Extract acceptance criteria from work item
3. Validate implementation against requirements
4. Check for regressions in related code
5. Perform adversarial chaos testing:
   - Invalid inputs, boundary conditions
   - Unexpected user flows
   - Security vulnerabilities (XSS, injection, auth bypass)
   - Race conditions, concurrent usage
   - Missing validation and error handling
6. Generate comprehensive QA report

**Tools:** Bash, Glob, Grep, Read

**Testing categories:**
- Input validation (empty, null, special chars, length limits)
- User flow attacks (skip steps, back/forward, refresh, concurrent access)
- State & race conditions (session expiry, network failures, concurrent updates)
- Authorization & security (unauthenticated access, role bypass, CSRF)
- Business logic edge cases (zero quantities, negative values, invalid combinations)

## Prerequisites for Scout & Chaos Monkey

Both Scout and Chaos Monkey agents require Azure CLI to fetch work items from Azure DevOps.

**Required setup:**
```bash
# Install Azure CLI (varies by OS)
brew install azure-cli  # macOS

# Install DevOps extension
az extension add --name azure-devops

# Authenticate
az login
az devops configure --defaults organization=https://norriq.visualstudio.com
```

**Agents will check:**
1. If `az` command exists
2. If `azure-devops` extension is installed (v1.0.2+)
3. If user is authenticated

**If prerequisites missing:**
- Agent will provide setup instructions
- Agent will stop execution until setup is complete
- User must complete setup and re-run agent

**Work item access:**
- Agents use: `az boards work-item show --id <id> --organization https://norriq.visualstudio.com`
- Requires user to have Azure DevOps access (already granted to team)
- Can extract: title, description, acceptance criteria, state, estimate, custom fields

**Note:** Bouncer and Sherlock do not require Azure CLI.

**Note:** Chaos Monkey was renamed from "Karen" in v0.3.3 for clarity and to align with industry-recognized chaos engineering terminology.

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

## Future Agent Ideas

Potential agents to add to this plugin based on NORRIQ Commerce team needs:

### High Value

**QA Validator ("Chaos Monkey")** ✅ **IMPLEMENTED** (v0.2.0, renamed v0.3.3)
- ~~Takes Azure DevOps user story/bug link as input~~
- ~~Fetches acceptance criteria from the work item~~
- ~~Validates that implementation matches all acceptance criteria~~
- ~~Checks for regression - searches for related code that might be affected~~
- ~~Performs chaos testing - systematically tries to break the feature~~
- ~~Generates comprehensive QA report~~
- **Status:** Live in plugin - see `agents/chaos-monkey.md`
- **Note:** Renamed from "Karen" to "Chaos Monkey" in v0.3.3 for clarity

**Test Generator ("Edison")**
- Generates unit tests for uncovered code
- Creates test stubs for new features
- Suggests test cases based on code analysis
- **Rationale:** Team currently has minimal test coverage - this could ease test adoption

**Bug Investigator ("Watson")**
- Analyzes stack traces and error logs
- Searches codebase for error sources
- Suggests fixes based on similar resolved issues
- Checks recent changes related to reported bugs
- **Rationale:** Speeds up production debugging and root cause analysis

**API Documentation Generator ("Scribe")**
- Generates/updates Swagger/OpenAPI documentation
- Creates example requests/responses
- Validates API contracts between frontend/backend
- Updates DTO documentation automatically
- **Rationale:** Keeps frontend/backend teams in sync, reduces integration issues

### Commerce-Specific

**Business Central Helper ("BC Buddy")**
- Helps write BC-compatible queries (avoiding OR operators, complex joins)
- Validates Business Central integration patterns
- Suggests retry logic and error handling for BC APIs
- Checks for BC query limitations before code review
- **Rationale:** NORRIQ uses Business Central with specific query limitations

**Performance Analyzer ("Flash")**
- Finds N+1 query patterns in backend
- Detects unnecessary re-renders in Vue components
- Suggests caching opportunities
- Analyzes bundle sizes and suggests optimizations
- Checks image optimization and lazy loading
- **Rationale:** Commerce sites need optimal performance for user experience

### Developer Experience

**Migration Assistant ("Upgrade")**
- Helps with framework upgrades (Nuxt 3→4, .NET 8→9)
- Identifies breaking changes in dependencies
- Suggests migration steps with code examples
- Validates compatibility across tech stack
- **Rationale:** Framework updates are time-consuming and risky

**Dependency Auditor ("Guard")**
- Checks for outdated npm packages and NuGet packages
- Scans for known security vulnerabilities
- Suggests safe upgrade paths with changelogs
- Identifies unused dependencies
- **Rationale:** Security and maintenance best practices

**Onboarding Guide ("Mentor")**
- Explains codebase architecture and patterns
- Finds examples of specific patterns in the codebase
- Answers "where should this code go?" questions
- Provides context about design decisions
- **Rationale:** Helps new team members ramp up quickly

### Implementation Priority

When adding new agents:
1. Start with agents that address immediate pain points
2. Validate agent usefulness with 2-3 team members first
3. Iterate based on feedback before wider rollout
4. Keep agents focused - one clear purpose per agent
5. Update CHANGELOG.md when adding new agents

## Reference Links

- Claude Code Plugin Docs: https://code.claude.com/docs/en/plugins
- Plugin Reference: https://code.claude.com/docs/en/plugins-reference
- Accelerator Frontend: `/Users/opc/Documents/git/Accelerator/Accelerator.Frontend`
- Accelerator API: `/Users/opc/Documents/git/Accelerator/Accelerator.Api`
