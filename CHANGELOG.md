# Changelog

All notable changes to the NORRIQ Commerce Agents plugin will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.3.2] - 2025-11-11

### Added
- **Azure CLI prerequisite checks** - Scout and Karen now verify Azure CLI setup before execution
- **Setup instructions** - Agents provide step-by-step Azure CLI installation and authentication guide if prerequisites are missing
- **Prerequisites documentation** - Added comprehensive setup guide in README and CLAUDE.md

### Changed
- Scout and Karen agents now gracefully handle missing Azure CLI
- Improved work item fetching with proper organization parameter

## [0.3.1] - 2025-11-11

### Changed
- **Renamed MacGyver to Scout** - More professional and clearer indication of reconnaissance/planning role
- Updated all documentation and references

## [0.3.0] - 2025-11-11

### Added
- **Scout agent** (originally named MacGyver) - Pre-implementation planning agent for speed date meetings
  - Fetches Azure DevOps user story details
  - Analyzes requirements and suggests implementation approach
  - Searches codebase for similar implementations to learn from
  - Performs impact analysis (what could break)
  - Generates pseudo code and implementation outline
  - Creates testing strategy
  - Identifies documentation requirements
  - Reviews and adjusts estimates based on complexity
  - Generates comprehensive speed date report covering all 7 talking points:
    1. How to solve the task
    2. Similar implementations in codebase
    3. What could break
    4. Testing strategy
    5. Documentation needs
    6. Pseudo code
    7. Estimate review
  - Designed to replace manual speed date preparation

## [0.2.0] - 2025-11-11

### Added
- **Karen agent** - QA validation agent for post-implementation testing
  - Fetches Azure DevOps work item details (user stories/bugs)
  - Validates implementation against acceptance criteria
  - Regression checking for related code
  - Adversarial "Karen testing" - tries to break features:
    - Invalid inputs, boundary conditions
    - Unexpected user flows
    - Security vulnerabilities (XSS, injection, auth bypass)
    - Race conditions and concurrent usage
    - Missing validation and error handling
  - Comprehensive QA report generation:
    - Acceptance criteria validation results
    - Critical issues and warnings
    - Security concerns
    - Suggested additional tests
  - Designed to catch issues before formal QA testing

## [0.1.0] - 2025-11-11

### Added
- **Bouncer agent** - Fast pre-PR validation agent (<1 minute)
  - Compilation checks (dotnet build, npm run lint)
  - Critical pattern detection for SSR/hydration issues
  - Async/await deadlock detection (.Result, .Wait())
  - Hardcoded secrets and API URL detection
  - Auto-generated file protection (swaggerApiClient.ts)
  - CRITICAL vs RECOMMENDATIONS reporting

- **Sherlock agent** - Thorough code review agent for PR reviewers
  - Deep architecture analysis (Clean Architecture, CQRS)
  - Fat MediatR handler detection (>100 lines)
  - Comprehensive security audit
  - Performance pattern analysis
  - Educational feedback with code examples
  - Positive observation highlighting

- **Frontend support**
  - Vue 3+, Nuxt 3+, Nuxt 4+ with Composition API
  - TypeScript pattern validation
  - Pinia state management checks
  - Auto-import verification
  - SSR/hydration issue detection
  - Legacy support (Vue 2, Nuxt 2 with graceful degradation)

- **Backend support**
  - .NET 8 with Clean Architecture
  - MediatR/CQRS pattern validation
  - Entity Framework/ElasticSearch query checks
  - Azure Key Vault secret management verification
  - Business Central integration considerations

- **Documentation**
  - README.md with usage instructions
  - CLAUDE.md with development context
  - Plugin manifest configuration

- **Version detection** - Automatic framework version detection with minimum requirements (Vue 3+, Nuxt 3+)

### Changed
- N/A (initial release)

### Deprecated
- N/A (initial release)

### Removed
- N/A (initial release)

### Fixed
- N/A (initial release)

### Security
- Agents actively scan for hardcoded secrets, API keys, and connection strings
- Detection of unsafe SSR patterns that could expose sensitive data

---

## Release Notes

### v0.1.0 - Initial Release

This is the first release of the NORRIQ Commerce Agents plugin. It introduces two specialized agents designed to improve code quality across the commerce department:

**Bouncer** serves as a gatekeeper for PRs, ensuring developers catch critical issues before code review. It's designed to be fast and focused on breaking issues only.

**Sherlock** assists reviewers by providing thorough analysis of code changes, helping maintain architecture standards and code quality across the team.

Both agents automatically detect project types and framework versions, adapting their checks appropriately for the stack in use.
