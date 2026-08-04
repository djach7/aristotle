# Phase 3: Generate Code Challenges

Instructions for generating code reading challenges.

## Format

Write all challenges to a single `code-challenges.md` file:

```markdown
# Code Challenges

## Challenge 1: <Title>
**Level:** 2
**Files:** <file1>, <file2>
**Task:** <what to read and explain>
**Key Points:**
- <point 1 the user should identify>
- <point 2>
- <point 3>

## Challenge 2: <Title>
...
```

## Requirements

- Generate approximately 1 challenge per level (skip Level 1 — it's too introductory)
- Each challenge must reference specific source files in the target repo
- Challenges should ask the user to read real code and explain what they see
- Focus on the most interesting or non-obvious code in each level's domain
- Key points are used to evaluate the user's answer — they don't need to hit every point, but should demonstrate understanding

## Challenge Design

Good challenges ask users to:
- Trace a function's execution path
- Explain why a particular implementation choice was made
- Identify a pattern or technique used in the code
- Describe how two modules interact
- Explain what would happen if a specific part changed

Bad challenges:
- Ask for trivia that requires memorization
- Require running the code
- Ask about code that wasn't covered in the curriculum
- Are too broad ("explain this entire module")

## Evaluation Guidelines

When evaluating a user's response to a challenge:
- Look for understanding of the core concepts, not exact terminology
- Give credit for partial understanding
- If they miss a key point, explain it without being condescending
- Mark the challenge as completed if they demonstrate reasonable understanding (don't require perfection)
