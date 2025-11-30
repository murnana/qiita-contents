# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Qiita content management repository for managing and publishing technical articles using Qiita CLI. Articles are stored as Markdown files in the `public/` directory and proofread using textlint for Japanese text.

## Key Commands

### Test and Lint
- `npm test` - Run textlint on all Markdown files in public directory
- `npx textlint "./public/*.md"` - Direct lint processing for Markdown content

### Qiita CLI Operations
- `npx qiita login` - Authenticate with Qiita (requires QIITA_TOKEN)
- `npx qiita preview` - Local preview (starts server on localhost:8888)
- `npx qiita publish` - Publish articles to Qiita
- `npx qiita pull` - Pull latest articles from Qiita
- `npx qiita new <filename>` - Create new article template

### Development Environment
- Unified development environment using DevContainer
- `npm ci` - Install dependencies (recommended over npm install for CI)

## Architecture and Structure

### Article Management
- Articles stored as Markdown files in `public/` directory
- Filenames correspond to Qiita article IDs
- Articles use YAML frontmatter for Qiita-specific metadata (title, tags, private status, etc.)
- Preview server settings controlled by `qiita.config.json`

### Text Quality Management
- Uses textlint with Japanese-specific rule presets for technical documents
- `.textlintrc` configuration supports Qiita-specific syntax like `:::details`
- Automated proofreading via GitHub Actions on pull requests

### CI/CD Workflow
- **Proofreading workflow** (`proofreading.yml`): Runs textlint on PRs, provides review comments via reviewdog
- **Publish workflow** (`publish.yml`): Automatically publishes articles to Qiita on main branch push
- Requires `QIITA_TOKEN` secret configuration for publishing

### Dependencies
- `@qiita/qiita-cli`: Official Qiita CLI for article management
- `textlint` with Japanese presets: Text linting for Japanese technical content
- `@anthropic-ai/claude-code`: Claude Code integration

### Image Management
- Images organized per-article in `assets/{article-slug}/` directory
- `sources/` subdirectory: Original editable assets (draw.io, raw screenshots)
- `images/` subdirectory: Optimized, publish-ready images (PNG, WebP, JPEG)
- Both sources and optimized images are tracked in Git
- Upload logs stored in `docs/development/asset-management/logs/{article-slug}.md`

#### Image Workflow
1. Create source images in `assets/{article-slug}/sources/`
2. Optimize images to `assets/{article-slug}/images/` (manual ffmpeg)
3. Upload to Qiita web interface (https://qiita.com/settings/images)
4. Record upload info in `docs/development/asset-management/logs/{article-slug}.md`
5. Reference Qiita URL in article markdown with Japanese alt text

See docs/development/asset-management/README.md for detailed instructions.

## Important Notes

- All article content in Japanese, following Japanese technical writing conventions
- textlint configuration optimized for Qiita Markdown extensions
- Publishing to main branch is automated - be careful with direct pushes
- Preview server runs on port 8888 by default (configurable in qiita.config.json)
- Qiita Terms of Service: https://qiita.com/terms
- Qiita Community Guidelines: https://help.qiita.com/ja/articles/qiita-community-guideline
- Qiita Markdown syntax: https://qiita.com/Qiita/items/c686397e4a0f4f11683d
- Qiita embeddable content: https://qiita.com/Qiita/items/612e2e149b9f9451c144
- Qiita CLI: https://github.com/increments/qiita-cli
