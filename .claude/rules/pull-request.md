# Pull Request Rules

## Language
- Write PR titles and descriptions in Japanese.

## Scope
- Each PR should address a single topic or purpose.
- Do not bundle unrelated changes into a single PR.

## Title Format
- Keep the title concise (under 50 characters if possible).
- Clearly describe what changed and why, not how.

## Description
- Summarize the purpose and background of the changes.
- List the main changes made.
- If the PR is related to a specific article, mention the article title or filename.

## Checklist Before Opening a PR
- Run `npm test` (textlint) and confirm zero errors.
- Verify that all reference URLs in articles are accessible.
- Ensure technical explanations are accurate.

## Branch Strategy
- Branch off from `main` for new features or articles.
- Use descriptive branch names (e.g., `feature/article-slug` or `fix/textlint-error`).

## Review and Merge
- Do not merge your own PR without review, unless the change is trivial (e.g., typo fix).
- Address all review comments before merging.
- Merge to `main` triggers automatic Qiita publication — confirm the article is ready before merging.

## Safety
- Never force-push to `main`/`master`.
- Do not close or reopen PRs to bypass CI checks.
