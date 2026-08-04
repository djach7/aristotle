---
name: aristotle
description: >-
  Turn any codebase into a structured learning curriculum. Analyzes a repo, generates
  progressive levels with reference material, quizzes, and code challenges. Supports
  multiple repos with switching. Use when the user wants to learn, study, or quiz
  themselves on any codebase.
---

# Aristotle — Learn Any Repo

A Claude Code skill that turns any codebase into a structured learning curriculum —
progressive lessons, quizzes, and code challenges generated from your actual source code.

## Arguments

`$ARGUMENTS` — One of the following modes:

```
/aristotle                          Status dashboard for active repo
/aristotle status                   Same as above
/aristotle init <repo-path>         Analyze a repo and generate curriculum
/aristotle teach <N>                Teach level N
/aristotle quiz <N>                 Quiz on level N (5 questions)
/aristotle quiz all                 Cumulative quiz across all completed levels
/aristotle code                     Code reading challenges
/aristotle plan                     Study schedule
/aristotle repos                    List all initialized repos
/aristotle switch <repo-slug>       Switch active repo
/aristotle refine <N>               Review/edit generated content for level N
/aristotle reset <repo-slug>        Reset progress (keep curriculum)
/aristotle rebuild <repo-slug>      Regenerate curriculum from scratch
/aristotle next                     Do the next logical step automatically
/aristotle remove <repo-slug>       Remove a repo's curriculum entirely
/aristotle help                     Show available commands
```

If `$ARGUMENTS` is empty, default to `status`.

## Data Layout

All generated data lives at `~/.aristotle/`:

```
~/.aristotle/
├── config.json                     # Active repo + all repos list
└── repos/
    └── <repo-slug>/
        ├── curriculum.json         # Level definitions, repo metadata
        ├── progress.json           # Learning progress
        └── references/
            ├── level-1-<slug>.md   # Generated reference material per level
            ├── level-N-<slug>.md
            ├── quiz-bank.md        # All quiz questions
            └── code-challenges.md  # Code reading challenges
```

## Config and Progress Schemas

### `~/.aristotle/config.json`
```json
{
  "active_repo": "oci-delta",
  "repos": {
    "oci-delta": {
      "path": "/home/user/repos/oci-delta",
      "name": "oci-delta",
      "initialized": "2026-08-04",
      "level_count": 6,
      "lesson_duration_minutes": 20
    }
  }
}
```

### Reading config:
```bash
mkdir -p ~/.aristotle
cat ~/.aristotle/config.json 2>/dev/null || echo '{"active_repo":"","repos":{}}'
```

### `curriculum.json`
```json
{
  "repo_name": "oci-delta",
  "repo_path": "/home/user/repos/oci-delta",
  "primary_language": "Go",
  "complexity": "medium",
  "lesson_duration_minutes": 20,
  "levels": [
    {
      "number": 1,
      "slug": "foundations",
      "title": "Foundations",
      "description": "What this project is, why it exists, key vocabulary",
      "pattern": "domain",
      "reference_file": "level-1-foundations.md",
      "source_files": ["README.md", "go.mod", "main.go"],
      "quiz_count": 5
    }
  ],
  "code_challenges_total": 5
}
```

### `progress.json`
```json
{
  "started_date": "2026-08-04",
  "last_activity": "2026-08-04",
  "levels": {
    "1": {
      "taught": "2026-08-04",
      "quiz_score": 4,
      "quiz_total": 5,
      "quiz_date": "2026-08-04"
    }
  },
  "code_challenges_completed": [],
  "code_challenges_total": 5
}
```

### Reading progress:
```bash
SLUG=$(jq -r '.active_repo' ~/.aristotle/config.json 2>/dev/null)
cat ~/.aristotle/repos/$SLUG/progress.json 2>/dev/null || echo '{"levels":{},"code_challenges_completed":[]}'
```

## Reference Files

This skill ships with reference files that guide curriculum generation:

| File | Purpose |
|------|---------|
| [references/level-patterns.md](references/level-patterns.md) | Universal level taxonomy — which patterns to use and when |
| [references/teaching-style.md](references/teaching-style.md) | Pedagogical guidelines for all teaching |
| [references/init-prompts/analyze-repo.md](references/init-prompts/analyze-repo.md) | How to analyze a repo in Phase 1 |
| [references/init-prompts/generate-level.md](references/init-prompts/generate-level.md) | How to generate reference content per level |
| [references/init-prompts/generate-quizzes.md](references/init-prompts/generate-quizzes.md) | How to generate quiz questions |
| [references/init-prompts/generate-challenges.md](references/init-prompts/generate-challenges.md) | How to generate code challenges |

## Workflow by Mode

### Mode: `init <repo-path>`

This is a 4-phase workflow. Load the corresponding init-prompt reference file for each phase.

#### Phase 1 — Analysis

Load [references/init-prompts/analyze-repo.md](references/init-prompts/analyze-repo.md) and follow its instructions.

1. Validate `<repo-path>` exists and is a git repo (check for `.git/`)
2. Explore the repo: file tree, line counts, README, build files, CI configs, entry points
3. Identify: primary language, build system, test framework, module structure
4. Assess complexity: `small` (<2k LOC, <20 files), `medium` (2-15k LOC), `large` (15-50k LOC), `very-large` (>50k LOC)
5. Derive a short slug from the repo directory name (lowercase, hyphens)

#### Phase 2 — Level Planning

Load [references/level-patterns.md](references/level-patterns.md).

6. Ask the user their preferred lesson duration using AskUserQuestion:
   - Options: 10 min, 15 min, 20 min (recommended), 25 min, 30 min
7. Compute level count based on complexity and duration:
   - Base content units by complexity: small=3, medium=5, large=7, very-large=9
   - Level count = `round(base * (20 / duration))`, clamped to [3, 10]
8. Select level topics using the pattern taxonomy:
   - Always start with `domain` (first level), always end with `expert` (last level)
   - Middle levels selected from: `architecture`, `core-logic` (can span 2 levels), `supporting`, `tooling`, `data-model`, `integrations`
   - Match patterns to what the repo actually contains — skip patterns that don't apply
9. Present the proposed curriculum as a table to the user, ask to approve or adjust:
   ```
   ## Proposed Curriculum for <repo-name>

   Complexity: medium | Lesson duration: 20 min | Levels: 5

   | Level | Pattern | Title | Key Topics |
   |-------|---------|-------|------------|
   | 1 | domain | Foundations | ... |
   | 2 | architecture | Architecture | ... |
   | ... | ... | ... | ... |

   Does this look good? You can adjust level count, reorder, or change topics.
   ```

#### Phase 3 — Content Generation

For each level, load [references/init-prompts/generate-level.md](references/init-prompts/generate-level.md):

10. Read the relevant source files for that level from the target repo
11. Generate a reference file (100-600 lines depending on level complexity) grounded in actual code
12. Write to `~/.aristotle/repos/<slug>/references/level-N-<slug>.md`

Then generate quizzes — load [references/init-prompts/generate-quizzes.md](references/init-prompts/generate-quizzes.md):

13. Generate 5 questions per level, mixed types (multiple choice, true/false, short answer, code identification)
14. Every question must reference real code, real function names, real behavior from the repo
15. Write to `~/.aristotle/repos/<slug>/references/quiz-bank.md`

Then generate challenges — load [references/init-prompts/generate-challenges.md](references/init-prompts/generate-challenges.md):

16. Generate ~1 code challenge per level, each referencing specific source files
17. Write to `~/.aristotle/repos/<slug>/references/code-challenges.md`

18. Write `curriculum.json` with all level definitions
19. Initialize `progress.json` with empty state

#### Phase 4 — Review

20. Show a summary:
    ```
    ## Curriculum Generated for <repo-name>

    | Level | Title | Reference Lines | Quiz Questions |
    |-------|-------|----------------|----------------|
    | 1 | Foundations | 150 | 5 |
    | ... | ... | ... | ... |

    Total: N levels, M quiz questions, K code challenges
    ```
21. Tell the user: "Review any level with `/aristotle refine N`, or start learning with `/aristotle teach 1`"

After init completes, update `~/.aristotle/config.json` to add this repo and set it as active.

### Mode: `status` (default)

1. Read config.json to get active repo
2. If no active repo: "No repos initialized yet. Run `/aristotle init <repo-path>` to get started."
3. Read curriculum.json and progress.json for the active repo
4. Display a dashboard:
   ```
   ## <Repo Name> — Learning Progress

   Level 1: Foundations        [taught] Quiz: 4/5
   Level 2: Architecture       [taught] Quiz: not taken
   Level 3: Core Logic         [not started]
   ...

   Code Challenges: 2/N completed

   Suggested next: /aristotle quiz 2
   ```
5. Suggest the next logical step:
   - If a level is taught but not quizzed → suggest the quiz
   - If a quiz scored < 4/5 → suggest retaking it
   - If all prior levels are complete → suggest the next teach
   - If all levels done → suggest code challenges

### Mode: `teach <N>`

1. Read curriculum.json to get level N's definition
2. Validate N is within range (1 to level_count)
3. Load the generated reference file for level N from `~/.aristotle/repos/<slug>/references/`
4. Load [references/teaching-style.md](references/teaching-style.md) for pedagogical guidelines
5. Present the content as an interactive lesson:
   - Use clear headers and structure from the reference
   - Read actual source files from the target repo and show real code snippets
   - After each major section, check understanding: "Does this make sense?" or "Any questions before we move on?"
   - End with a summary of key takeaways
6. Update progress.json to mark the level as taught with today's date
7. Update last_activity in progress.json
8. Suggest: "Ready to test your knowledge? Run `/aristotle quiz N`"

### Mode: `quiz <N>`

1. Read curriculum.json to validate level N
2. Check progress.json — if level N hasn't been taught, say: "You haven't studied Level N yet. Run `/aristotle teach N` first."
3. Load `quiz-bank.md` from `~/.aristotle/repos/<slug>/references/`, find questions for level N
4. Present questions ONE AT A TIME using AskUserQuestion:
   - Multiple choice: present options as choices
   - True/false: present True and False as options
   - Short answer: present as free text (use the "Other" option capability)
   - Code identification: show a code snippet and ask what it does
5. After each answer:
   - Correct: confirm and briefly explain why
   - Incorrect: explain the correct answer with context from the actual code
6. After all 5 questions:
   - Show final score: "Score: X/5"
   - If score >= 4: "Great job! You've mastered this level."
   - If score < 4: "Consider reviewing the material: `/aristotle teach N`"
7. Update progress.json with score and date
8. Suggest next step

### Mode: `quiz all`

1. Read progress.json to find all taught levels
2. Select 2 questions from each taught level from the quiz bank
3. Follow the same quiz flow as `quiz <N>` but with the cumulative set
4. Show per-level breakdown in results

### Mode: `code`

1. Read progress.json and curriculum.json
2. Load `code-challenges.md` from `~/.aristotle/repos/<slug>/references/`
3. Present the next uncompleted challenge (or let user pick)
4. For each challenge:
   - Read the relevant source files from the target repo
   - Show key code excerpts
   - Ask the user to explain what they see
   - Evaluate their answer and provide feedback
   - Mark the challenge as completed in progress.json

### Mode: `next`

1. Read config.json, curriculum.json, and progress.json for the active repo
2. If no active repo: run `init` flow (same as having no repos)
3. Determine the next logical step using the same logic as the `status` suggestion:
   - If a level is taught but not quizzed → run that quiz
   - If a quiz scored < 4/5 → suggest retaking (ask first, since they may prefer to move on)
   - If all prior levels are complete → teach the next level
   - If all levels and quizzes done → run the next code challenge
   - If everything is complete → say so and suggest trying another repo
4. Execute the determined step directly (don't just display it — actually run the teach/quiz/code flow)

### Mode: `plan`

1. Read curriculum.json for level count and titles
2. Generate a study schedule based on level count:
   - Compute total days needed: ~2 days per level (teach + quiz) + review days + challenge days
   - Format as a weekly schedule table
3. Display the schedule using actual level titles from the curriculum

### Mode: `repos`

1. Read config.json
2. Display all initialized repos with progress summary:
   ```
   ## Initialized Repos

   * oci-delta (active) — 4/6 levels, 2/6 challenges
     Path: ~/rfe/temp/oci-delta

   * my-project — 1/4 levels, 0/3 challenges
     Path: ~/projects/my-project
   ```

### Mode: `switch <repo-slug>`

1. Read config.json
2. Validate the slug exists in repos
3. Update `active_repo` to the new slug
4. Show status dashboard for the newly active repo

### Mode: `refine <N>`

1. Read the generated reference file for level N
2. Display it to the user
3. Ask what they'd like to change (add content, remove content, adjust depth, fix errors)
4. Apply the changes and rewrite the reference file
5. Optionally regenerate quiz questions for that level

### Mode: `reset <repo-slug>`

1. Validate the slug exists
2. Reset progress.json to empty state (keep curriculum.json and references intact)
3. Confirm: "Progress reset for <repo-name>. Curriculum and reference material preserved."

### Mode: `rebuild <repo-slug>`

1. Validate the slug exists
2. Warn: "This will regenerate all curriculum content and clear progress. Continue?"
3. Re-run the init workflow (Phases 1-4) using the stored repo path
4. Clear progress.json

### Mode: `remove <repo-slug>`

1. Validate the slug exists in config.json
2. Warn: "This will delete all generated curriculum, quizzes, challenges, and progress for <repo-name>. The repo itself at `<path>` will not be touched. Continue?"
3. Delete `~/.aristotle/repos/<slug>/` directory
4. Remove the repo entry from config.json
5. If the removed repo was `active_repo`, set `active_repo` to another repo (if any exist) or empty string
6. Confirm: "Removed curriculum for <repo-name>. The repo at `<path>` is unchanged."

### Mode: `help`

Display the command reference:
```
## Aristotle — Commands

| Command | Description |
|---|---|
| /aristotle | Status dashboard for active repo |
| /aristotle init <path> | Analyze a repo and generate curriculum |
| /aristotle teach <N> | Teach level N |
| /aristotle quiz <N> | Quiz on level N (5 questions) |
| /aristotle quiz all | Cumulative quiz across all completed levels |
| /aristotle code | Code reading challenges |
| /aristotle plan | Study schedule |
| /aristotle repos | List all initialized repos |
| /aristotle switch <slug> | Switch active repo |
| /aristotle refine <N> | Review/edit generated content for level N |
| /aristotle reset <slug> | Reset progress (keep curriculum) |
| /aristotle rebuild <slug> | Regenerate curriculum from scratch |
| /aristotle next | Do the next logical step automatically |
| /aristotle remove <slug> | Remove a repo's curriculum entirely |
| /aristotle help | This message |
```

## Teaching Style

Load [references/teaching-style.md](references/teaching-style.md) for detailed guidelines. Key principles:

- Be a patient, knowledgeable colleague — not a textbook
- Use analogies to make complex concepts accessible
- Always ground explanations in the actual code — read from the target repo and show real snippets
- Connect each concept to WHY it matters for the project
- Anticipate common misconceptions
- Build on prior levels — reference concepts from earlier levels when relevant

## Error Handling

- **No active repo:** "No repos initialized. Run `/aristotle init <repo-path>` to analyze a codebase."
- **Repo path not found:** "Can't find a repo at `<path>`. Check the path and try again."
- **Repo not a git repo:** "The directory at `<path>` doesn't appear to be a git repository."
- **Invalid level:** "Levels are 1-N. Run `/aristotle status` to see where you are."
- **Level not taught when quizzing:** "You haven't studied Level N yet. Run `/aristotle teach N` first."
- **Unknown repo slug:** "No repo called '<slug>'. Run `/aristotle repos` to see available repos."
- **Progress/config file missing:** Create with empty defaults, don't error
- **Already initialized:** "This repo is already initialized. Use `/aristotle rebuild <slug>` to regenerate, or `/aristotle reset <slug>` to clear progress."
