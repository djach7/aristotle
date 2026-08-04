# Phase 3: Generate Quiz Bank

Instructions for generating quiz questions for all curriculum levels.

## Format

Write all questions to a single `quiz-bank.md` file with this structure:

```markdown
# Quiz Bank

## Level 1: <Title>

### Q1.1 [multiple_choice]
**Question:** <question text>
- A) <option>
- B) <option>
- C) <option>
- D) <option>
**Answer:** B
**Explanation:** <why B is correct, referencing actual code>

### Q1.2 [true_false]
**Question:** <statement>
**Answer:** False
**Explanation:** <why it's false, with code reference>

### Q1.3 [short_answer]
**Question:** <question requiring a brief explanation>
**Answer:** <expected answer>
**Explanation:** <full explanation with code reference>

### Q1.4 [code_id]
**Question:** What does this code do?
```<language>
<real code snippet from the repo>
```
- A) <option>
- B) <option>
- C) <option>
- D) <option>
**Answer:** C
**Explanation:** <explanation>

## Level 2: <Title>
...
```

## Requirements

- **5 questions per level**, exactly
- **Type distribution per level:** aim for a mix of at least 3 different types across the 5 questions
- **Code grounding:** every question must reference real behavior, real functions, or real code from the repo
- **Difficulty progression:** questions within a level should go from straightforward to challenging (Q1 easiest, Q5 hardest)
- **No trick questions:** test understanding, not memorization of obscure details
- **Short answer grading:** the answer field should capture the key concept, not demand exact wording. When grading, look for understanding of the core idea.

## Verification

Before finalizing each question:
1. Re-read the referenced code to confirm the answer is correct
2. Confirm that wrong multiple-choice options are plausible but clearly wrong
3. Confirm that the explanation adds value beyond just restating the answer

## Question Types

| Type | Delivery via AskUserQuestion |
|------|------------------------------|
| `multiple_choice` | Present options as choices |
| `true_false` | Present True and False as choices |
| `short_answer` | User types answer in "Other" field |
| `code_id` | Show code snippet, present options as choices |
