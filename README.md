# Decentraland Documentation

This repository contains all documentation for the Decentraland ecosystem, organized as a GitBook-powered monorepo with multiple documentation spaces.

## 📚 Documentation Spaces

This repository hosts four separate GitBook spaces, each with its own purpose and audience:

### 🎮 [Player Documentation](player/)
Documentation for Decentraland users and players. Covers gameplay, avatars, marketplace, DAO participation, and in-world features.

- **Audience**: End users, players
- **Topics**: Getting started, blockchain integration, DAO, marketplace, in-world features
- **GitBook Space**: `player`

### 🎨 [Creator Documentation](creator/)
Complete guide for content creators building experiences in Decentraland. Includes Scene Editor, SDK7, wearables, emotes, and 3D modeling.

- **Audience**: Scene creators, wearable designers, game developers
- **Topics**: Scene Editor, SDK7, wearables, emotes, 3D modeling, publishing
- **GitBook Space**: `creator`

### 💻 [Contributor Documentation](contributor/)
Technical documentation for developers contributing to Decentraland's open-source codebase.

- **Audience**: Core developers, open-source contributors
- **Topics**: Architecture, authentication, content system, scene runtime, communications, testing standards
- **GitBook Space**: `contributor`

### 🔌 [API Reference](apis/)
Comprehensive API documentation for all Decentraland backend services.

- **Audience**: Developers integrating with Decentraland services
- **Topics**: RESTful APIs, authentication, service endpoints, data models
- **GitBook Space**: `apis`

## 🏗️ Repository Structure

```
docs/
├── README.md                    # This file
├── .gitbook.yaml               # GitBook configuration (root)
│
├── player/                      # Player documentation space
│   ├── README.md               # Player landing page
│   ├── SUMMARY.md              # Player navigation/sidebar
│   ├── images/                 # Player images
│   └── ...                     # Player content pages
│
├── creator/                     # Creator documentation space
│   ├── README.md               # Creator landing page
│   ├── SUMMARY.md              # Creator navigation/sidebar
│   ├── images/                 # Creator images
│   └── ...                     # Creator content pages
│
├── contributor/                 # Contributor documentation space
│   ├── README.md               # Contributor landing page
│   ├── SUMMARY.md              # Contributor navigation/sidebar
│   ├── images/                 # Contributor images
│   └── ...                     # Contributor content pages
│
└── apis/                        # API reference space
    ├── README.md               # APIs landing page
    ├── SUMMARY.md              # APIs navigation/sidebar
    └── ...                     # API documentation pages
```

## 🔄 GitBook Integration

### How It Works

This repository uses [GitBook's GitHub Sync](https://docs.gitbook.com/integrations/git-sync) to automatically publish documentation changes.

1. **Write**: Contributors edit markdown files in this repository
2. **Review**: Changes are reviewed via GitHub Pull Requests
3. **Merge**: Approved changes are merged to the `main` branch
4. **Deploy**: GitBook automatically syncs and publishes the updated documentation

### GitBook Configuration

Each documentation space has its own `.gitbook.yaml` configuration file (or shares the root configuration) that defines:

- **Root directory**: Where the documentation lives (e.g., `player/`, `creator/`)
- **Structure**: Entry point (`README.md`) and navigation (`SUMMARY.md`)
- **Redirects**: URL redirects for moved pages (optional)

Example `.gitbook.yaml`:

```yaml
root: ./player/

structure:
  readme: README.md
  summary: SUMMARY.md
```

### Automatic Deployment

✅ **Every merge to `main` triggers an automatic deployment**

When you merge a pull request to the `main` branch:
1. GitHub notifies GitBook via webhook
2. GitBook pulls the latest changes
3. GitBook rebuilds and publishes all affected spaces
4. Changes are live within minutes

## 📝 Writing Documentation

### Markdown Guidelines

- Use standard [GitHub Flavored Markdown](https://github.github.com/gfm/)
- GitBook supports additional features:
  - `{% hint style="info|warning|danger|success" %}` for callout boxes
  - `{% embed url="..." %}` for embedded content (videos, etc.)
  - Mermaid diagrams with triple backtick code blocks

### Navigation Structure

Each space has a `SUMMARY.md` file that defines the sidebar navigation:

```markdown
# Table of contents

* [Welcome](README.md)

## Section Name

* [Page Title](path/to/page.md)
* [Another Page](path/to/another.md)
  * [Nested Page](path/to/nested.md)
```

**GitBook Limitations:**
- Maximum 2 levels of nesting (groups and pages)
- Groups are defined by `##` headings
- Nested items use indentation

### Images

Store images in the `images/` directory within each space:

```
player/images/
creator/images/
contributor/images/
apis/images/
```

Reference images using relative paths:

```markdown
![Alt text](images/subfolder/image.png)
```

## 🤝 Contributing

### Quick Start

1. **Clone the repository**
   ```bash
   git clone https://github.com/decentraland/docs.git
   cd docs
   ```

2. **Create a branch**
   ```bash
   git checkout -b feature/your-improvement
   ```

3. **Make changes**
   - Edit markdown files in the appropriate space
   - Add images to the `images/` directory
   - Update `SUMMARY.md` if adding/removing pages

4. **Test locally** (optional)
   - Preview markdown files in your editor
   - Or use GitBook's local preview

5. **Submit a pull request**
   ```bash
   git add .
   git commit -m "docs: Description of changes"
   git push origin feature/your-improvement
   ```

6. **Preview your changes**
   - When you open a PR to the `main` branch, GitBook automatically generates a preview deployment
   - Look for the GitBook check in the PR status — it will include a link to preview your changes
   - Use this preview to verify formatting, links, and overall presentation before requesting review

### PR Preview

Every pull request to `main` triggers GitBook's preview integration:

| Step | What Happens |
|------|--------------|
| PR Opened | GitBook builds a preview version of the documentation with your changes |
| Preview Ready | A unique preview URL is generated and posted in the PR checks |
| Review | Reviewers can see exactly how changes will look when published |
| Merge | Once approved and merged, changes deploy to the live documentation |

This preview feature ensures that documentation changes can be thoroughly reviewed in their final rendered form before going live.

## 🔗 Links

- **Live Documentation**: [docs.decentraland.org](https://docs.decentraland.org)
- **GitBook**: [Decentraland GitBook Organization](https://app.gitbook.com/)

## 📜 License

This documentation is licensed under the [Apache License 2.0](LICENSE).
