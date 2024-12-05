# Kuibu - A Learning Journey Blog

Welcome to Kuibu ("small steps" in Chinese), a personal blog documenting my learning journey and knowledge sharing. This blog is built using [Obsidian](https://obsidian.md/) for content management, [Quartz](https://quartz.jzhao.xyz/) for static site generation, and [GitHub Pages](https://pages.github.com/) for hosting.

> This setup is based on Nicole van der Hoeven's excellent tutorial: [How to publish Obsidian notes with Quartz on GitHub Pages](https://notes.nicolevanderhoeven.com/How+to+publish+Obsidian+notes+with+Quartz+on+GitHub+Pages)

## About

"不积跬步，无以至千里" - Without accumulating small steps, one cannot reach a thousand miles.

This blog represents my commitment to continuous learning and sharing knowledge. Each post is a small step in my journey of understanding and growth.

## Prerequisites

Before setting up this blog, ensure you have:
- NodeJS v18.14+ (`node -v` to check)
- NPM v9.3.1+ (`npm -v` to check)
- Git (`git --version` to check)
- Obsidian
- GitHub account

## Setup Instructions

### 1. Install Quartz

```bash
# Clone Quartz repository
git clone https://github.com/jackyzha0/quartz.git my-notes
cd my-notes

# Install dependencies
npm i

# Initialize Quartz
npx quartz create
```

When prompted:
1. Select "Empty Quartz"
2. Choose "Treat links as shortest path" for link resolution

### 2. GitHub Repository Setup

1. Create a new GitHub repository
2. Update remote origin:
```bash
git remote -v
git remote rm origin
git remote add origin https://github.com/yourusername/my-notes.git
```

3. Sync your changes:
```bash
npx quartz sync --no-pull
```

### 3. Obsidian Setup

1. Open Quartz folder as vault in Obsidian
2. Create note template with required frontmatter:
```yaml
---
title: "Your Title Here"
draft: false
tags:
  - 
---
```

### 4. Local Development

Build and preview your site:
```bash
npx quartz build --serve
```
Visit `http://localhost:8080` to see your site.

### 5. Deployment

1. Create GitHub Actions workflow file:
```bash
mkdir -p .github/workflows
touch .github/workflows/deploy.yml
```

2. Add the deployment configuration (see tutorial for detailed yml content)

3. Enable GitHub Pages:
- Go to repository Settings > Pages
- Set source to GitHub Actions

### Directory Structure

```
.
├── content/          # All blog posts and content
├── public/          # Static assets
├── quartz/         # Quartz configuration and components
└── README.md       # This file
```

### Regular Maintenance

Update your site:
```bash
# Build the site
npx quartz build

# Sync changes to GitHub
npx quartz sync
```

## Optional: Custom Domain Setup

If using a custom domain:
1. Purchase domain from a registrar
2. Add A records pointing to GitHub Pages
3. Configure custom domain in GitHub Pages settings
4. Enable HTTPS
