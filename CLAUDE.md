# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Purpose

This is a collection of specialized agents for Claude Code that enforce Test-Driven Development (TDD) and Domain-Driven Design (DDD) practices. The repository itself does not contain executable code - it contains agent definitions (markdown files) that are symlinked into user projects.

## Architecture Overview

**Agent-Based System**: Unlike auto-discovered skills, this uses explicitly-invokable agents with isolated contexts and restricted tool access for disciplined development workflows.

**Multi-Domain Organization**:
- `development/` - TDD agents (language-specific test/code writers, test runner, DDD architect)
- `shared/` - Reference materials (TDD principles, DDD patterns, examples)
- `documentation/`, `devops/`, `data-science/`, `planning/` - Placeholder directories for future agent categories

**Language-Specific Approach**: Separate agents for TypeScript (`*-ts.md`) and Python (`*-py.md`) to reduce context size (~250 lines vs ~450 combined) and provide focused, framework-specific guidance (Vitest vs pytest).

## Key Design Principles

**Strict TDD Enforcement**: All development agents follow the Red-Green-Refactor cycle with 80% minimum coverage across all metrics (lines, branches, functions, statements). Agents will refuse to write implementation without failing tests first.

**Domain-Driven Design**: All code follows DDD tactical patterns:
- Entities (unique identity, mutable)
- Value Objects (immutable, attribute-based equality)
- Aggregates (transactional boundaries with one root entity)
- Repositories (persistence abstraction at aggregate level)
- Domain Services (multi-aggregate coordination)

**Layered Architecture**: Domain Layer (pure business logic) → Application Layer (use cases) → Infrastructure Layer (implementations) → Presentation Layer (API/UI)

## Agent Workflow

The typical TDD workflow using these agents:

1. **Domain Modeling** (optional): `/ddd_architect "Design [feature] aggregate"` - Get guidance on bounded contexts, entity vs value object decisions
2. **RED Phase**: `/test_writer_{ts|py} "Test [feature]"` - Write failing tests
3. **Verify Failure**: `/test_runner` - Confirm tests fail for the right reason
4. **GREEN Phase**: `/code_writer_{ts|py} "Implement [feature]"` - Minimal code to pass tests
5. **Verify Success**: `/test_runner` - Ensure tests pass with 80%+ coverage
6. **REFACTOR Phase**: `/code_writer_{ts|py} "Refactor [improvement]"` - Improve code while keeping tests green
7. **Verify Green**: `/test_runner` - Confirm refactoring didn't break anything

## SSH and Git Permission Workflow

### 1Password Integration

SSH keys and git credentials are managed through 1Password, requiring manual approval for each connection.

**Critical Pattern**: When a task requires SSH or git operations, attempt connections **early** (not at the last moment) so the user can approve 1Password prompts and step away from the PC.

**Good Example**:
```bash
# Early in the task, test connections
ssh exterminator echo "Connected"
git fetch --dry-run
gh auth status

# User approves 1Password prompts

# Now proceed with the actual work
```

**Bad Example**:
```bash
# Doing all the work first...
# (30 minutes of coding)

# Finally trying to push at the end
git push  # Blocks waiting for user approval!
```

### SSH Access Patterns

When tasks require SSH connections to remote systems:
- Use SSH config aliases for convenience (defined in `~/.ssh/config`)
- Test connections early in tasks: `ssh [alias] echo "Connected"`
- Wait for 1Password approval before proceeding with actual work
- Document project-specific SSH **aliases** in the project's CLAUDE.md (not IP addresses or usernames)
- Connection details (host, user) should remain in `~/.ssh/config` (not tracked in git)

### GitHub CLI

The `gh` CLI tool is installed and available in all repositories under `../`:
- `gh pr create` - Create pull requests
- `gh pr view` - View PR details
- `gh issue list` - List issues
- `gh repo view` - View repository info

When tasks involve GitHub operations, test `gh auth status` early to ensure authentication is approved.

## Common Commands

Since this is an agent repository (not an application), there are no build/test commands for the repository itself. The agents are used in target projects where they execute:

**TypeScript Projects**:
- `vitest` - Run tests in watch mode
- `vitest run` - Run tests once
- `vitest --coverage` - Run with coverage report

**Python Projects**:
- `pytest` - Run tests
- `pytest --cov` - Run with coverage
- `pytest --cov --cov-report=html` - Generate HTML coverage report
- `uv run pytest --cov` - Run with uv (if using uv package manager)

## File Organization Patterns

**TypeScript Test Structure**:
```
src/
├── components/
│   ├── BlogCard.tsx
│   └── __tests__/
│       └── BlogCard.test.tsx
```

**Python Test Structure**:
```
backend/
├── domain/
│   └── user.py
└── tests/
    └── domain/
        └── test_user.py
```

## Agent Installation in Target Projects

Users symlink agents into their `.claude/agents/` directory:

**TypeScript Project**:
```bash
mkdir -p .claude/agents
cd .claude/agents
ln -s /path/to/agentic-workflows/development/tdd/test-writer-ts.md
ln -s /path/to/agentic-workflows/development/tdd/code-writer-ts.md
ln -s /path/to/agentic-workflows/development/tdd/test-runner.md
```

**Python Project**: Same as above but use `*-py.md` variants

**Monorepo**: Symlink both TypeScript and Python variants

## Reference Materials Location

- TDD principles and best practices: `shared/tdd/tdd-principles.md`
- DDD patterns reference: `shared/tdd/ddd-patterns.md`
- Code examples: `shared/examples/` (entity-example.ts, value-object-example.ts, aggregate-example.ts)
- Test templates: `shared/tdd/test-templates/`

## Agent Behavior Expectations

**Test Writers** (`test-writer-{ts|py}.md`):
- Create comprehensive tests following AAA pattern (Arrange, Act, Assert)
- Test happy paths, edge cases, error conditions
- Use framework-specific best practices (Vitest/React Testing Library for TS, pytest/pytest-asyncio for Python)
- Never write implementation code

**Code Writers** (`code-writer-{ts|py}.md`):
- Read failing tests first to understand requirements
- Implement minimal code to pass tests (no over-engineering)
- Apply DDD patterns (entities, value objects, aggregates)
- Follow language-specific conventions (JSDoc for TS, type hints for Python)
- Refuse to write code without existing failing tests

**Test Runner** (`test-runner.md`):
- Auto-detect framework (Vitest vs pytest) based on project files
- Execute tests and report pass/fail status
- Display coverage metrics (must meet 80% threshold)
- Provide actionable suggestions when coverage falls short

**DDD Architect** (`ddd-architect.md`):
- Guide bounded context identification
- Help decide entity vs value object
- Define aggregate boundaries and invariants
- Provide implementation patterns for domain patterns

## Coverage Configuration Examples

**Vitest** (vitest.config.ts):
```typescript
export default defineConfig({
  test: {
    coverage: {
      provider: 'v8',
      thresholds: { lines: 80, functions: 80, branches: 80, statements: 80 }
    }
  }
});
```

**pytest** (pyproject.toml):
```toml
[tool.coverage.report]
fail_under = 80
show_missing = true
```

## Important Constraints

1. **Tests First, Always**: Never implement before writing failing tests
2. **80% Coverage Minimum**: All metrics (lines, branches, functions, statements) must meet threshold
3. **One Aggregate Per Repository**: Repository pattern applies only to aggregate roots
4. **Immutable Value Objects**: Value objects cannot be modified after creation
5. **Aggregate Invariants**: Business rules enforced by aggregate root, not external services
6. **No Anemic Domain Model**: Business logic belongs in domain entities, not services

## Ubiquitous Language

When working in this repository or using these agents, use consistent terminology:
- "Agent" not "skill" (these are explicitly-invoked, isolated-context workflows)
- "Red-Green-Refactor" not "test-code-refactor" (standard TDD terminology)
- "Aggregate Root" not "parent entity" (DDD-specific term)
- "Value Object" not "immutable object" (DDD pattern name)
- "Repository" refers to domain persistence abstraction, not version control

## Creating CLAUDE.md for Consumer Projects

When creating CLAUDE.md files for projects that use these agents (like lingodrift, akpandeya.com, oracle-infrastructure), include these sections:

### Recommended Template Structure

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

## TDD Agents

This project uses agents from the agentic-workflows repository:
- `/test_writer_{ts|py}` - Write tests first (RED phase)
- `/code_writer_{ts|py}` - Implement minimal code (GREEN phase)
- `/test_runner` - Verify coverage and test status
- `/ddd_architect` - Domain modeling guidance

See `../agentic-workflows/CLAUDE.md` for complete agent documentation.

## Related Repositories

[List other related repositories that may need context during debugging]

- `../agentic-workflows` - TDD/DDD agent definitions
- `../[other-repo]` - [Purpose]

## SSH and Git Permissions

This project uses 1Password for SSH/git credential management.

**Permission Pattern**: Always test SSH and git connections early in tasks:
```bash
# Test connections at the start
ssh [host-alias] echo "Connected"
git fetch --dry-run
gh auth status

# Wait for 1Password approval
# Proceed with actual work
```

### SSH Configuration

[Project-specific SSH access details, e.g.:]
- Remote host: `[ssh-alias]`
- Purpose: [what this connection is for]

## Common Commands

[Project-specific build/test/deploy commands]

## Architecture

[Project-specific architecture notes]
```

### Example for lingodrift (LingoDrift)

```markdown
# CLAUDE.md - lingodrift (LingoDrift Language Learning Platform)

## TDD Agents
Uses agents from `../agentic-workflows`:
- `/test_writer_ts` - Vitest tests for TypeScript/React
- `/code_writer_ts` - TypeScript implementation with DDD patterns
- `/test_runner` - Run Vitest with coverage
- `/ddd_architect` - Domain modeling for exam/attempt aggregates

## Related Repositories
- `../agentic-workflows` - TDD/DDD agent definitions
- `../oracle-infrastructure` - Deployment infrastructure

## SSH and Git Permissions
Uses 1Password for credential management. Test connections early:
```bash
ssh exterminator echo "Connected"  # Oracle infrastructure
gh auth status                      # GitHub CLI
```

## Common Commands
- `npm run dev` - Start development server
- `npm run test` - Run Vitest tests
- `npm run test:coverage` - Run with coverage report
- `npm run build` - Production build

## Domain Model
**Bounded Contexts:**
- Exam Management - Create and manage language exams
- Attempt Tracking - Track user test attempts and scores
- User Management - User profiles and subscriptions

**Key Aggregates:**
- Exam (root: Exam, entities: Section, Question)
- Attempt (root: Attempt, entities: Answer)
- User (root: User, entities: Profile)
```

## Future Expansion Areas

Placeholder directories indicate planned agent categories:
- Refactoring agents (pattern application, code improvement)
- Debugging agents (performance profiling, bug investigation)
- Code review agents (quality analysis, security checks)
- Documentation agents (README, API docs, technical writing)
- DevOps agents (Docker, Kubernetes, CI/CD)
- Data Science agents (ML pipelines, data analysis)
- Planning agents (project breakdown, architecture decisions)
