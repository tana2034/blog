# Blog - Local Development Environment

This repository contains a blog site built with [Hugo](https://gohugo.io/) using the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme.

## Prerequisites

- [Hugo](https://gohugo.io/) 0.148.2+
- Git

## Setup Instructions

### 1. Clone the Repository

```bash
git clone <repository-url>
cd blog
```

### 2. Setup Theme

```bash
# Clone the theme (first time only)
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

### 3. Install Dependencies

If Hugo is not installed:

```bash
# macOS (Homebrew)
brew install hugo

# Verify installation
hugo version
```

## Start Development Server

```bash
# Start development server (including draft posts)
hugo server -D
```

Once the server is running, open your browser and visit:
- http://localhost:1313/

The browser will automatically refresh when you make changes to files.

## Build

```bash
# Build for production
hugo

# Output directory: public/
```

## Creating New Posts

```bash
# Create a new post
hugo new docs/YYYY/MM/article-name.md

# Edit the post
# Open content/docs/YYYY/MM/article-name.md and edit
```

Example front matter:
```yaml
---
title: "Your Post Title"
date: 2025-01-01T00:00:00+09:00
draft: false
description: "Brief description of the post"
tags: ["tag1", "tag2"]
categories: ["category"]
---

Write your content here.
```

## Directory Structure

```
.
├── config.toml          # Site configuration
├── content/             # Content files
│   ├── _index.md       # Homepage content
│   ├── search.md       # Search page
│   └── docs/           # Blog posts
├── themes/             # Themes
│   └── PaperMod/      # PaperMod theme
└── public/             # Build output (auto-generated)
```

## Features

- **Search**: Full-text search functionality at `/search/`
- **SEO**: Optimized with structured data, meta tags, and sitemaps
- **Fast**: Lightweight and performant theme
- **Responsive**: Mobile-friendly design
- **Dark/Light Mode**: Automatic theme switching
- **Code Highlighting**: Syntax highlighting for code blocks
- **Social Integration**: GitHub and email links in header

## Configuration

- Site config: `config.toml`
- Theme: [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
- Language: Japanese (ja)
- Search: Fuse.js powered full-text search

## Troubleshooting

### Theme not found

```bash
# Re-fetch the theme
rm -rf themes/PaperMod
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod
```

### Server won't start

1. Check Hugo version: `hugo version`
2. Verify `config.toml` syntax
3. Ensure `themes/PaperMod` directory exists

### Search not working

1. Ensure JSON output is enabled in `config.toml`
2. Check that `content/search.md` exists
3. Verify search is added to main menu

## Deployment

This blog is deployed on Netlify.
- Production URL: https://tana2034-blog.netlify.com/
- Auto-deploy on push to `main` branch