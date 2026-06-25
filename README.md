# Kushal's Portfolio

[![Netlify Status](https://api.netlify.com/api/v1/badges/3bd50ceb-1df5-4c54-b0af-a9a58234bf65/deploy-status)](https://app.netlify.com/projects/kushal-patil/deploys)

Personal portfolio and blog built with Astro. I write about voice AI, RAG pipelines, Rust, distributed systems, and whatever else I'm learning.

## Stack

- **[Astro](https://astro.build)** — static site generator
- **[Astro Cactus](https://github.com/chrismwilliams/astro-theme-cactus)** — theme
- **Tailwind CSS v4** — styling
- **[Expressive Code](https://expressive-code.com/)** — syntax highlighting
- **[Satori](https://github.com/vercel/satori)** — auto-generated OG images
- **Netlify** — hosting and deploys

## Local Development

```bash
pnpm install
pnpm dev
```

Site runs at `http://localhost:4321`.

## Writing a Post

Add a `.md` file to `src/content/post/`:

```markdown
---
title: "Your Post Title"
description: "A short description."
publishDate: "2026-06-25"
tags: ["tag-one", "tag-two"]
pinned: false
---

Post content here.
```

Set `pinned: true` to feature the post on the homepage.

## Writing a Note

Add a `.md` file to `src/content/note/`:

```markdown
---
title: "Short Note"
publishDate: "2026-06-25T00:00:00Z"
pinned: false
---

Note content here.
```

## Commands

| Command          | Action                                      |
| :--------------- | :------------------------------------------ |
| `pnpm install`   | Install dependencies                        |
| `pnpm dev`       | Start dev server at `localhost:4321`        |
| `pnpm build`     | Build for production into `./dist/`         |
| `pnpm postbuild` | Build Pagefind static search index          |
| `pnpm preview`   | Preview production build locally            |