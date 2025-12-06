# Qiita Contents Management

A content management repository for managing and publishing technical articles to Qiita using Qiita CLI. Articles are stored as Markdown files in the `public/` directory with Japanese text proofreading using textlint.

## Features

- 📝 Article management with Qiita CLI
- 🔍 Japanese text proofreading with textlint
- 🚀 Automated publishing to Qiita via GitHub Actions
- 🐳 DevContainer support for consistent development environment
- 📋 Pull request review automation with reviewdog

## Quick Start

### Prerequisites

- Node.js (specified in `.devcontainer/devcontainer.json`)
- Qiita account and API token

### Installation

```bash
npm ci
```

### Qiita Authentication

```bash
npx qiita login --credential ./.qiita-config/
```

Authentication credentials will be automatically saved to `./.qiita-config/` and persist across DevContainer rebuilds.

## Usage

### Article Management

```bash
# Create a new article
npx qiita new <filename>

# Preview articles locally (http://localhost:8888)
npx qiita preview

# Pull latest articles from Qiita
npx qiita pull

# Publish articles to Qiita
npx qiita publish
```

### Text Proofreading

```bash
# Run textlint on all articles
npm test

# Run textlint directly
npx textlint "./public/*.md"
```

## Project Structure

```
qiita-contents/
├── .github/workflows/     # CI/CD workflows
│   ├── publish.yml       # Auto-publish to Qiita on main branch
│   └── proofreading.yml  # PR text proofreading with reviewdog
├── .devcontainer/        # DevContainer configuration
├── assets/               # Article images and source files
│   └── {article-slug}/
│       ├── sources/      # Original editable assets
│       └── images/       # Optimized publish-ready images
├── docs/                 # Documentation
│   └── development/
│       └── asset-management/
│           ├── README.md # Image management guide
│           └── logs/     # Image upload logs
├── public/               # Article content (Markdown files)
├── .textlintrc          # textlint configuration
├── qiita.config.json    # Qiita CLI configuration
└── package.json         # Project dependencies
```

## Image Management

Images are organized per-article in the `assets/` directory. Both source files and optimized images are tracked in Git.

**Workflow:**
1. Create source images (draw.io diagrams, screenshots) in `assets/{article-slug}/sources/`
2. Optimize images using ffmpeg to `assets/{article-slug}/images/`
3. Manually upload to Qiita via web interface
4. Record upload info in `docs/development/asset-management/logs/{article-slug}.md`
5. Reference returned Qiita URLs in article markdown

See [docs/development/asset-management/README.md](docs/development/asset-management/README.md) for detailed instructions.

## Article Format

Articles use YAML front matter for Qiita-specific metadata:

```markdown
---
title: "Article Title"
emoji: "📝"
type: "tech" # or "idea"
topics: ["javascript", "nodejs"]
published: false
---

# Article Content

Your article content goes here...
```

## CI/CD Workflows

### Proofreading Workflow

- Triggered on pull requests
- Runs textlint for Japanese text validation
- Provides review comments via reviewdog

### Publishing Workflow

- Triggered on pushes to main branch
- Automatically publishes articles to Qiita
- Requires `QIITA_TOKEN` secret in repository settings

## Text Linting Rules

The project uses textlint with Japanese-specific presets:

- `textlint-rule-preset-japanese`: Basic Japanese grammar rules
- `textlint-rule-preset-jtf-style`: JTF (Japan Technical Writers Association) style guide
- `textlint-rule-preset-ja-technical-writing`: Technical writing rules for Japanese
- Custom rules for Qiita-specific syntax (e.g., `:::details`)

## Development Environment

This project includes a DevContainer configuration for consistent development across different machines. The container includes all necessary tools and extensions for content creation and editing.

## Configuration Files

- `.textlintrc`: textlint rules and settings
- `qiita.config.json`: Qiita CLI configuration (preview server port, etc.)
- `.devcontainer/devcontainer.json`: Development container setup

## Contributing

1. Create a feature branch
2. Write or edit articles in the `public/` directory
3. Run `npm test` to check text quality
4. Create a pull request
5. Address any textlint issues highlighted by reviewdog
6. Merge to main branch for automatic publication

## License

MIT

## Author

Murnana

## Repository

- GitHub: [murnana/qiita-contents](https://github.com/murnana/qiita-contents)
- Issues: [GitHub Issues](https://github.com/murnana/qiita-contents/issues)