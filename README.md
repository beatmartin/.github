# Centralized Workflows & Standards (Demo)

This repository contains reusable GitHub Actions workflows designed to enforce code quality, standardized Git practices, and Pull Request hygiene across all projects. By centralizing these rules, we ensure a consistent history and clean collaboration across multiple repositories.

## [i] Core Features

* **PR Metadata Validation:** Checks Pull Request titles and descriptions.
* **Commit History Validation:** Analyzes every commit message within a Pull Request.
* **Conventional Commits Enforcement:** Strictly requires the `type(optional-scope): description` format.
* **Language Enforcement (English):** Requires all Git history and PR metadata to be written in English. Automatically detects and blocks text containing Spanish-specific characters (e.g., `ñ`, `á`, `é`, `í`, `ó`, `ú`, `¿`, `¡`). 
    >This does not obviate the need to review the language yourself. It is worth noting that detecting these characters as errors is not the same as detecting the language itself; as this is an ongoing process, this aspect remains quite weak.
* **Automated Feedback:** Leaves automated comments on invalid PRs detailing the exact errors and providing CLI instructions to fix them.

---

## [i] How to Use (Destination Repositories)

To apply these rules to another repository, you do not need to rewrite the scripts. Simply create a workflow file (e.g., `.github/workflows/pr-validation.yml`) in your destination repository and "call" these central workflows.

```yaml
name: PR Validation Suite

on:
  pull_request:
    types: [opened, edited, synchronize]

permissions:
  pull-requests: write

jobs:
  check-pr-metadata:
    uses: beatmartin/.github/.github/workflows/check-pr-metadata.yml@main

  check-commits:
    needs: check-pr-metadata
    uses: beatmartin/.github/.github/workflows/check-commits.yml@main

```

---

## [i] Standard Rules

### 1. Conventional Commits

All commits and PR titles must follow the Conventional Commits specification:

```text
<type>[optional scope]: <description>

# Examples:
feat(api): add user authentication endpoint
fix(ui): resolve button alignment issue
docs: update installation guide in README

```

**Allowed Types:**

* `feat`: A new feature
* `fix`: A bug fix
* `docs`: Documentation only changes
* `style`: Changes that do not affect the meaning of the code (white-space, formatting)
* `refactor`: A code change that neither fixes a bug nor adds a feature
* `perf`: A code change that improves performance
* `test`: Adding missing tests or correcting existing tests
* `build`: Changes that affect the build system or external dependencies
* `ci`: Changes to our CI configuration files and scripts
* `chore`: Other changes that don't modify src or test files
* `revert`: Reverts a previous commit

### 2. Language Policy

To maintain an international standard, all descriptions, titles, and commit messages **must be in English**. The workflows will automatically reject any text containing Spanish characters or punctuation.
>Due to varying results, the code rejects characters found in the Spanish alphabet—such as accented vowels and the letter "ñ", though this was primarily implemented to avoid errors involving numbers and symbols. Consequently, this automation does not eliminate the need to carefully review every pull request and commit; for the time being, it focuses on structure rather than language.

---

## [i] Troubleshooting & Fixing Errors

If your Pull Request is blocked by the bot, read the automated comment to identify the problematic commit(s) or text.

### Fixing PR Metadata (Title & Body)

1. Click the **Edit** button on your PR in the GitHub web interface.
2. Fix the formatting or translate the text to English.
3. Click **Save**. The workflow will automatically re-run.

### Fixing Commit History

If the error is in your commit messages, you must rewrite your local git history and force-push.

**To fix only the latest commit:**

```bash
git commit --amend
git push --force

```

**To fix older or multiple commits:**

```bash
# Replace N with the number of commits you want to review
git rebase -i HEAD~N

```

1. Change `pick` to `reword` (or `r`) next to the commits you need to fix.
2. Save and close the editor. Git will prompt you to rename each selected commit.
3. Once finished, force push to update the PR:

```bash
git push --force

```