# Proofreading Rules

## Requirement
All articles must pass `npm test` (textlint) with zero errors before committing.

## Common Issues to Watch

### Doubled particles (`no-doubled-joshi`)
- Avoid repeating the same particle (e.g., `に`) in a single sentence.
- Example fix: `コンパイル時に即座に` → `コンパイル時点で即座に`

### Doubled conjunctive words
- Avoid using the same conjunction consecutively.

### Spacing around inline code/links
- Do not add unnecessary spaces around inline code or Markdown links in Japanese sentences.

## Footnotes
- Use Qiita footnote syntax: place `[^id]` inline and define `[^id]: text` at the end of the article (after the reference section).
- Do not place footnote definitions inline in the middle of the article body.
