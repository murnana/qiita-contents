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

## Technical Accuracy
- Verify that technical explanations are correct before finalizing an article.
- If unsure about a technical claim, note the uncertainty explicitly or look it up.
- When describing behavior of tools or libraries (e.g., Unity, Emscripten, IL2CPP), cross-check against official documentation.

## Source URLs
- Verify that all reference URLs are accessible and point to the intended content.
- Prefer versioned or stable URLs (e.g., specific version docs) over generic links that may change.
- Do not guess or fabricate URLs. Only include URLs that have been confirmed to exist.

## Footnotes
- Use Qiita footnote syntax: place `[^id]` inline and define `[^id]: text` at the end of the article (after the reference section).
- Do not place footnote definitions inline in the middle of the article body.
