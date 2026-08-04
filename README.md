# aristotle

A Claude Code skill that turns any codebase into a structured learning curriculum — progressive lessons, quizzes, and code challenges generated from your repo.

## Quick Start

1. Clone this repo into your Claude Code skills directory (this is where Claude Code looks for skills — any repo cloned here becomes available as a `/slash` command):
   ```bash
   git clone https://github.com/djach7/aristotle.git ~/.claude/skills/aristotle
   ```

2. Point it at a repo you want to learn:
   ```
   /aristotle init ~/path/to/some-repo
   ```

3. Aristotle walks you through setup:
   - Analyzes the codebase (language, complexity, module structure)
   - Asks how long you want each lesson to be (10-30 min)
   - Proposes a curriculum with the right number of levels for the repo
   - Lets you adjust before generating content

4. Start learning:
   ```
   /aristotle teach 1
   ```

5. From here, `/aristotle next` will always pick up where you left off — no need to remember what level you're on.

## How It Works

Aristotle reads your codebase — the actual source files, not just the README — and builds a curriculum tailored to the repo's size and complexity:

- **Small repos** (< 2k LOC) get 3 levels
- **Medium repos** (2-15k LOC) get 4-6 levels
- **Large repos** (15k+ LOC) get 6-10 levels

Level count also adjusts to your preferred lesson duration. Want 15-minute lessons? More levels, each covering less. Prefer 25-minute deep dives? Fewer, meatier levels.

Every level follows a pattern from a universal taxonomy — Foundations, Architecture, Core Logic, Supporting Systems, etc. — but the content is always grounded in your repo's actual code.

## Commands

| Command | What it does |
|---|---|
| `/aristotle` | Status dashboard |
| `/aristotle init <path>` | Analyze a repo and generate curriculum |
| `/aristotle teach <N>` | Teach level N |
| `/aristotle quiz <N>` | Quiz on level N (5 questions) |
| `/aristotle quiz all` | Cumulative quiz across all levels |
| `/aristotle code` | Code reading challenges |
| `/aristotle plan` | Study schedule |
| `/aristotle repos` | List all initialized repos |
| `/aristotle switch <slug>` | Switch active repo (slug = repo directory name, e.g. `oci-delta`) |
| `/aristotle next` | Do the next logical step automatically |
| `/aristotle refine <N>` | Review/edit generated content |
| `/aristotle reset <slug>` | Reset progress (keep curriculum) |
| `/aristotle rebuild <slug>` | Regenerate curriculum from scratch |
| `/aristotle remove <slug>` | Remove a repo's curriculum entirely |
| `/aristotle help` | Show available commands |

## Multi-Repo Support

Learn multiple codebases simultaneously. Each repo gets its own curriculum, progress tracking, and quiz bank:

```
/aristotle init ~/projects/backend-api
/aristotle init ~/projects/frontend-app
/aristotle repos              # see both
/aristotle switch backend-api # switch context
```

Generated data lives at `~/.aristotle/repos/<slug>/`, separate from this skill.

## What Gets Generated

For each repo, Aristotle creates:

- **Reference files** (100-600 lines each) — structured notes grounded in actual code, one per level
- **Quiz bank** — 5 questions per level, mixed types (multiple choice, true/false, short answer, code identification)
- **Code challenges** — hands-on exercises that ask you to read and explain real code from the repo

All generated content references real functions, real files, and real line numbers from your codebase.

## Example

```
$ /aristotle init ~/repos/oci-delta

## Repo Analysis: oci-delta
Language: Go | LOC: ~8,500 | Files: 45 | Complexity: medium

? How long would you like each lesson to be?
> 20 minutes (Recommended)

## Proposed Curriculum (5 levels, ~20 min each)
| Level | Title              | Key Topics                    |
|-------|--------------------|-------------------------------|
| 1     | Foundations        | OCI concepts, delta purpose   |
| 2     | Architecture       | Repo layout, data flows       |
| 3     | Core Logic         | create, apply, import         |
| 4     | Supporting Systems | storage, ostree, verification |
| 5     | CLI & Testing      | commands, CI, test patterns   |
| 6     | Expert             | tradeoffs, limitations        |

Does this look good? You can adjust level count, reorder, or change topics.
> Looks good!

Generating reference material... done (6 levels, 30 quiz questions, 5 code challenges)
Start with /aristotle teach 1, or just run /aristotle next
```

## Requirements

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code) CLI or IDE extension
- A git repository to learn
