# Claude TDD Agents

Reusable Claude Code agents for strict Test-Driven Development (TDD) and Domain-Driven Design (DDD) workflows.

## What are Claude Code Agents?

Agents are explicitly-invokable assistants with specialized knowledge and tools. Unlike skills (which Claude discovers automatically), agents run in isolated contexts with restricted tool access, perfect for complex multi-step workflows like TDD/DDD.

## Agents Included

### 1. test-writer
Write comprehensive tests BEFORE implementation

- **Enforces:** Strict TDD (tests first, always)
- **Coverage:** Minimum 80% required
- **Patterns:** AAA (Arrange, Act, Assert)
- **Frameworks:** Vitest, pytest
- **Invocation:** `/test_writer`

### 2. code-writer
Implement code following TDD red-green-refactor cycle

- **Enforces:** Tests must exist and fail first
- **Patterns:** DDD (entities, value objects, aggregates)
- **Architecture:** Clean architecture layers
- **Quality:** SOLID principles, self-documenting code
- **Invocation:** `/code_writer`

### 3. test-runner
Execute tests and provide detailed feedback

- **Runs:** Vitest, pytest with coverage
- **Reports:** Pass/fail, coverage metrics
- **Analyzes:** Failures with suggestions
- **Modes:** Watch mode for TDD workflow
- **Invocation:** `/test_runner`

### 4. ddd-architect
Guide domain modeling and architecture decisions

- **Designs:** Bounded contexts, aggregates
- **Patterns:** Entities, value objects, repositories
- **Architecture:** Layered architecture
- **Events:** Domain events and CQRS
- **Invocation:** `/ddd_architect`

## Installation

### Option 1: Symlink to Projects (Recommended)

Symlinks keep agents updated automatically across all projects.

```bash
# In your project directory
mkdir -p .claude/agents

# Create symlinks to agents
cd .claude/agents
ln -s /path/to/claude-tdd-agents/agents/test-writer.md test-writer.md
ln -s /path/to/claude-tdd-agents/agents/code-writer.md code-writer.md
ln -s /path/to/claude-tdd-agents/agents/test-runner.md test-runner.md
ln -s /path/to/claude-tdd-agents/agents/ddd-architect.md ddd-architect.md
```

### Option 2: Copy Agents

```bash
# Copy agents directory
cp -r /path/to/claude-tdd-agents/agents .claude/
```

## Usage

### Invoke Agents

Use slash commands to explicitly invoke agents:

```bash
/test_writer     # Write tests for new feature
/code_writer     # Implement code (after tests exist)
/test_runner     # Run tests and show coverage
/ddd_architect   # Get domain modeling guidance
```

### TDD Workflow

1. **RED:** Invoke `/test_writer` to create failing tests
2. **GREEN:** Invoke `/code_writer` to implement minimal code
3. **VERIFY:** Invoke `/test_runner` to confirm tests pass
4. **REFACTOR:** Invoke `/code_writer` to improve code quality
5. **REPEAT:** Continue red-green-refactor cycle

### Example Session

```
User: "I need to add user authentication to my app"

User: /ddd_architect
→ Agent designs User aggregate, AuthService, value objects

User: /test_writer
→ Agent writes comprehensive tests for User entity

User: /code_writer
→ Agent implements User entity to make tests pass

User: /test_runner
→ Agent runs tests, confirms 87% coverage, all green

User: /code_writer refactor authentication logic
→ Agent improves code while keeping tests green
```

## Configuration

### Vitest Projects (TypeScript/JavaScript)

```json
{
  "scripts": {
    "test": "vitest",
    "test:ui": "vitest --ui",
    "test:coverage": "vitest --coverage",
    "test:run": "vitest run"
  },
  "devDependencies": {
    "vitest": "^4.0.0",
    "@vitest/ui": "^4.0.0",
    "@vitest/coverage-v8": "^4.0.0",
    "jsdom": "^27.0.0"
  }
}
```

```typescript
// vitest.config.ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
    environment: 'jsdom',
    coverage: {
      provider: 'v8',
      reporter: ['text', 'json', 'html', 'lcov'],
      thresholds: {
        lines: 80,
        functions: 80,
        branches: 80,
        statements: 80,
      },
    },
  },
});
```

### pytest Projects (Python)

```bash
# Install pytest with coverage
uv add --dev pytest pytest-cov pytest-asyncio

# Run tests
uv run pytest --cov --cov-report=html
```

```ini
# pyproject.toml or setup.cfg
[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]

[tool.coverage.run]
source = ["src"]
omit = ["*/tests/*", "*/test_*.py"]

[tool.coverage.report]
fail_under = 80
show_missing = true
```

## Requirements

- **Claude Code CLI:** Latest version
- **Testing Framework:** Vitest (TypeScript) or pytest (Python)
- **Coverage Tool:** @vitest/coverage-v8 or pytest-cov
- **Minimum Coverage:** 80% (all metrics)
- **CI/CD:** GitHub Actions (optional)

## Project Structure

```
claude-tdd-agents/
├── agents/                        # Agent definitions
│   ├── test-writer.md            # Test writing agent (YAML + Markdown)
│   ├── code-writer.md            # Code implementation agent
│   ├── test-runner.md            # Test execution agent
│   └── ddd-architect.md          # Domain modeling agent
├── shared/                        # Reference materials
│   ├── tdd-principles.md         # TDD best practices
│   ├── ddd-patterns.md           # DDD pattern reference
│   └── test-templates/           # Test templates
│       ├── vitest-unit.template.ts
│       ├── vitest-integration.template.ts
│       └── pytest-unit.template.py
├── examples/                      # DDD code examples
│   ├── entity-example.ts         # Entity pattern
│   ├── value-object-example.ts   # Value object pattern
│   └── aggregate-example.ts      # Aggregate pattern
└── README.md                      # This file
```

## Agent Behavior

### Strict TDD Enforcement

Agents enforce TDD discipline:

- **test-writer:** Refuses to write implementation code
- **code-writer:** Requires failing tests before implementing
- **test-runner:** Validates coverage meets 80% threshold
- **ddd-architect:** Guides proper domain modeling

### Isolated Contexts

Each agent runs in its own context with specific tools:

- **test-writer:** Read, Edit, Write, Bash, Glob, Grep
- **code-writer:** Read, Edit, Write, Bash, Glob, Grep
- **test-runner:** Bash, Read, Grep, Glob
- **ddd-architect:** Read, Glob, Grep

### Self-Documenting Code

All agents follow the principle of self-documenting code:

- Clear, descriptive names
- No unnecessary comments
- Comments only for business logic
- Functions/variables explain intent

## Best Practices

1. **Always write tests first** - Invoke `/test_writer` before `/code_writer`
2. **Small iterations** - Test one small piece of functionality at a time
3. **Run tests frequently** - Use `/test_runner` after every change
4. **Domain modeling first** - Consult `/ddd_architect` for complex features
5. **Maintain green builds** - Never commit failing tests
6. **Refactor when green** - Improve code only after tests pass
7. **80% minimum coverage** - All code must be tested

## Shared Resources

The `shared/` directory contains reference materials:

- **tdd-principles.md:** Red-Green-Refactor cycle, AAA pattern, coverage requirements
- **ddd-patterns.md:** Entities, value objects, aggregates, repositories, domain events
- **test-templates/:** Reusable test templates for common patterns

## Examples

The `examples/` directory contains concrete DDD implementations:

- **entity-example.ts:** User entity with business logic
- **value-object-example.ts:** Email, Money, Address, DateRange value objects
- **aggregate-example.ts:** Order aggregate with OrderLine child entities

## Integration with Projects

### Create claude.md in Your Project

Document your project's TDD/DDD workflow:

```markdown
# Claude Code Guide for MyProject

## Agents Available

- `/test_writer` - Generate comprehensive tests
- `/code_writer` - Implement code following TDD/DDD
- `/test_runner` - Execute tests and check coverage
- `/ddd_architect` - Domain modeling guidance

## Domain Model

**Bounded Contexts:**
- User Management
- Product Catalog
- Order Processing

**Key Aggregates:**
- User (root: User, entities: Profile, Preferences)
- Product (root: Product, entities: Variant, Review)
- Order (root: Order, entities: OrderLine, Payment)
```

### Update GitHub Actions

```yaml
name: CI/CD Pipeline

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run test:run
      - run: npm run test:coverage

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploy only after tests pass"
```

## Contributing

Agents are designed to be strict and opinionated to enforce TDD/DDD best practices. Changes should maintain rigor while improving usability.

## License

MIT - Use freely in your projects

## Support

For help:

1. **Agent Documentation:** Read `.md` files in `agents/`
2. **Shared Resources:** Check `shared/` for TDD/DDD reference
3. **Examples:** See `examples/` for DDD patterns
4. **Templates:** Use `shared/test-templates/` for test structure
5. **Issues:** Report at https://github.com/akpandeya/claude-tdd-agents/issues
