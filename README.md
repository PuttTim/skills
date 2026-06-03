# Skills

Personal skill definitions for AI coding assistants. Each directory contains a `SKILL.md` that defines specialized instructions and workflows for specific tasks.

## Install

```sh
npx skills@latest add putttim/skills
```

## Skills

| Skill | Description |
|-------|-------------|
| **[cheatsheet-creator](cheatsheet-creator/)** | Create high-density, exam-ready cheatsheets from lecture notes, PDFs, and slides. Outputs Typst documents compiled to PDF. |
| **[cheatsheet-optimiser](cheatsheet-optimiser/)** | Iteratively optimize cheatsheets through coverage checks, layout compression, readability tuning, and quick-reference additions. |
| **[commit](commit/)** | Analyze the working tree and commit changes as small, atomic, conventional commits. Never pushes. |
| **[commit-pr](commit-pr/)** | Commit atomically, then create or update a pull request for the current branch. |
| **[diff-main-summary](diff-main-summary/)** | Generate a markdown PR-review summary diffing the current branch against `main`. |
