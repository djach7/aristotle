# Phase 3: Generate Level Reference Content

Instructions for generating the reference material file for a single curriculum level.

## Input

You will have:
- The level's pattern (from level-patterns.md): domain, architecture, core-logic, etc.
- The level's title and description from the curriculum plan
- The list of source files to focus on
- The full repo analysis from Phase 1

## Process

1. **Read the source files** listed for this level from the target repo
2. **Identify the key concepts** — what must someone understand after this level?
3. **Write the reference file** following the structure below

## Reference File Structure

```markdown
# Level N: <Title>

## <Section 1 Title>

<Explanation of the concept, grounded in actual code.>

<Real code snippet from the repo:>

```<language>
// from <file-path>, lines X-Y
<actual code>
```

<Explain what the code does and why it matters.>

## <Section 2 Title>
...

## <Section N Title>
...
```

## Guidelines by Pattern

### domain
- Start with the problem this project solves — who uses it and why
- Define every domain-specific term (don't assume prior knowledge)
- Include a "glossary" section at the end with key vocabulary
- Read the README and any docs/ directory for context
- Target: 100-200 lines

### architecture
- Start with a high-level directory tree (annotated)
- Show the data flow: input → processing → output
- Identify the entry point and trace the startup path
- Map module dependencies
- Target: 150-300 lines

### core-logic
- Pick 2-4 functions that are the heart of what this project does
- Walk through each function's logic step by step
- Show actual code with surrounding context
- Explain non-obvious implementation choices
- Target: 200-500 lines

### supporting
- Cover shared types, utility functions, abstractions
- Show how core logic depends on these supporting pieces
- Include interface definitions and their implementations
- Target: 200-400 lines

### tooling
- CLI structure, build targets, CI pipeline
- Test patterns: what's tested, how, test helpers
- Dependencies and their purpose
- Target: 150-300 lines

### data-model
- Core types/structs/classes with field explanations
- Serialization formats, schemas, configs
- How data transforms as it flows through the system
- Target: 150-300 lines

### integrations
- External API calls, protocol handling
- System interfaces (filesystem, network, OS)
- Third-party library usage patterns
- Error handling at boundaries
- Target: 200-400 lines

### expert
- Design tradeoffs: what was chosen and what was rejected
- Known limitations and workarounds
- Extension scenarios: "how would you add X?"
- Performance characteristics and edge cases
- Target: 300-600 lines

## Quality Checks

- Every code snippet must be real code from the repo (read it, don't fabricate)
- Every claim about behavior must be verifiable in the source
- Include file paths and line ranges for all code references
- Don't repeat content from other levels — reference them instead ("As we saw in Level 2...")
- Write for someone who has completed all prior levels
