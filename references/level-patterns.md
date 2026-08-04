# Level Pattern Taxonomy

Universal patterns for structuring a codebase learning curriculum. Each pattern describes
a category of knowledge. Not every pattern applies to every repo — select based on what
the repo actually contains.

## Patterns

### `domain` — Always First
What this project is, why it exists, the problem it solves, key domain vocabulary.
Every repo starts here. Read the README, docs, and top-level comments. Identify the
core concepts someone needs before they can understand any code.

### `architecture` — Repo Layout & Data Flow
How the repo is organized: directory structure, module boundaries, entry points, how
data flows through the system. Include dependency graph and build system overview.
Use for medium+ complexity repos. Skip for very small single-file projects.

### `core-logic` — The Heart of the Code
The 2-4 most important functions or algorithms. The code that does the actual work.
Walk through the logic step by step with real code. Can span 2 levels for complex repos
(e.g., "Core Logic: Processing" and "Core Logic: Output").
Always include at least one core-logic level.

### `supporting` — Helper Systems
Shared types, utility modules, abstractions that the core logic depends on. Storage
layers, adapters, converters, validators. Use for medium+ repos where significant
supporting infrastructure exists.

### `tooling` — CLI, Build, CI, Tests
Command-line interface, build system (Makefile, package.json scripts), CI/CD pipeline,
test patterns and helpers. Use for repos with non-trivial build/test infrastructure.

### `data-model` — Types, Schemas, Structures
Core data types, database schemas, configuration formats, protocol definitions.
Use when the repo is data-heavy or when understanding the types is prerequisite to
understanding the logic.

### `integrations` — External Boundaries
External APIs, protocols, I/O boundaries, third-party library usage, system interfaces.
Use when the repo has significant integration surface (databases, APIs, OS interfaces, CGo).

### `expert` — Always Last
Design tradeoffs, known limitations, extension scenarios, edge cases, performance
characteristics. "How would you add X?" and "Why was Y designed this way?" questions.
Always the final level — requires understanding from all prior levels.

## Selection Rules

1. Always include `domain` as Level 1
2. Always include `expert` as the last level
3. Always include at least one `core-logic` level
4. For `small` repos (3 levels): domain → core-logic → expert
5. For `medium` repos (4-6 levels): add `architecture` and/or `supporting`/`tooling`
6. For `large`/`very-large` repos (6-10 levels): use most patterns, split `core-logic` across 2 levels
7. Order should follow dependency: understand architecture before core logic, core logic before supporting systems
8. Skip patterns that don't apply — don't force a `data-model` level on a repo with no significant types

## Duration Scaling

Base content units by complexity:
- `small`: 3 units
- `medium`: 5 units
- `large`: 7 units
- `very-large`: 9 units

Level count formula: `round(base_units * (20 / user_duration_minutes))`, clamped to [3, 10]

Examples:
- Medium repo, 20 min lessons → 5 levels
- Medium repo, 15 min lessons → 7 levels (same content, smaller pieces)
- Large repo, 25 min lessons → 6 levels (more content per lesson)
