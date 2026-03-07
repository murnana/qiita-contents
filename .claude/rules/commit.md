# Commit Rules

## Language
- Write commit messages in Japanese.

## Scope
- Commit changes separately by topic/purpose. Do not bundle unrelated changes into a single commit.

## Message Format
- Keep the subject line concise (under 50 characters if possible).
- Focus on "what changed and why", not "how".

## File Staging
- Stage specific files by name. Do not use `git add -A` or `git add .`.
- Do not commit files that may contain secrets (e.g., `.env`, credentials).

## Safety
- Never skip hooks (`--no-verify`).
- Never amend a published commit. Create a new commit instead.
- Never force-push to `main`/`master`.
