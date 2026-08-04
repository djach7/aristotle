# Phase 1: Repo Analysis

Instructions for analyzing a target repository during `/aristotle init`.

## Steps

1. **Validate the path**
   - Check that `<repo-path>` exists
   - Check for `.git/` directory
   - If either fails, report the error and stop

2. **Explore the file tree**
   ```bash
   find <repo-path> -type f -not -path '*/.git/*' -not -path '*/vendor/*' -not -path '*/node_modules/*' | head -200
   ```

3. **Count lines of code** (excluding vendored/generated)
   ```bash
   find <repo-path> -type f \( -name '*.go' -o -name '*.py' -o -name '*.js' -o -name '*.ts' -o -name '*.rs' -o -name '*.java' -o -name '*.c' -o -name '*.cpp' -o -name '*.rb' \) -not -path '*/vendor/*' -not -path '*/node_modules/*' -not -path '*/.git/*' | xargs wc -l 2>/dev/null | tail -1
   ```

4. **Identify the primary language**
   Look for build/config files in priority order:
   - `go.mod` → Go
   - `Cargo.toml` → Rust
   - `package.json` → JavaScript/TypeScript (check for `.ts` files)
   - `pyproject.toml` / `setup.py` / `requirements.txt` → Python
   - `pom.xml` / `build.gradle` → Java
   - `Gemfile` → Ruby
   - `Makefile` alone → check file extensions

5. **Read key files**
   - README (README.md, README.rst, README)
   - Build config (go.mod, package.json, Cargo.toml, etc.)
   - Makefile or equivalent
   - CI configs (.github/workflows/, .gitlab-ci.yml, Jenkinsfile)
   - Entry point (main.go, src/main.rs, index.js, app.py, etc.)

6. **Identify module structure**
   - Top-level directories that contain source code
   - Package/module boundaries
   - Test file locations and patterns

7. **Assess complexity**
   | Complexity | Files | LOC | Modules |
   |-----------|-------|-----|---------|
   | small | <20 | <2,000 | 1-2 |
   | medium | 20-80 | 2,000-15,000 | 3-8 |
   | large | 80-300 | 15,000-50,000 | 8-20 |
   | very-large | >300 | >50,000 | >20 |

   Use the highest complexity that matches at least 2 of the 3 columns.

8. **Derive slug**
   Take the repo directory basename, lowercase it, replace spaces/underscores with hyphens.
   Example: `oci-delta`, `my-project`, `fastapi-app`

## Output

Present findings to the user as a brief summary before moving to Phase 2:
```
## Repo Analysis: <name>

Language: Go | LOC: ~8,500 | Files: 45 | Complexity: medium
Build: go build with Makefile | Tests: go test | CI: GitHub Actions (4 jobs)
Entry point: main.go → cmd/root.go (cobra)
Key modules: pkg/oci-delta/ (core), cmd/ (CLI), vendor/ (dependencies)
```
