# Portfolio Context — Persistent Memory

## Owner
- **Name:** Kushalgouda Patil
- **Role:** Software Engineer at Ultraviolette
- **Site:** https://kushalpatil.in/
- **GitHub:** https://github.com/Kushalgouda-Patil/
- **LinkedIn:** https://www.linkedin.com/in/kushalgouda-patil/
- **Email:** kushalgouda.patil@ultraviolette.com

---

## Tech Stack
- **Framework:** Astro (static site generator) — theme: [Astro Cactus](https://github.com/chrismwilliams/astro-cactus)
- **Styling:** Tailwind CSS
- **Language:** TypeScript
- **Code highlighting:** Expressive Code (Shiki-based) — themes: `dracula` (dark), `github-light` (light)
- **OG images:** Satori (auto-generated per post)
- **Icons:** `@iconify-json/mdi` via astro-icon
- **Webmentions:** supported via `src/utils/webmentions.ts`
- **Fonts:** Roboto Mono (700 + regular), in `src/assets/`
- **Package manager:** pnpm (with pnpm-workspace.yaml)

---

## Site Configuration — `src/site.config.ts`
```
siteConfig.title       = "Kushalgouda Patil"
siteConfig.author      = "Kushalgouda Patil"
siteConfig.description = "Software Engineer at Ultraviolette"
siteConfig.url         = "https://kushalpatil.in/"
siteConfig.lang        = "en-GB"
siteConfig.ogLocale    = "en_GB"
siteConfig.date        = { locale: "en-GB", options: { day, month (short), year } }
```

**Nav links (menuLinks):** Home `/` · About `/about/` · Blog `/posts/` · Notes `/notes/`

---

## Content Collections — `src/content.config.ts`
| Collection | Path | Schema highlights |
|---|---|---|
| `post` | `src/content/post/**/*.{md,mdx}` | title, description, publishDate, updatedDate?, draft (default false), tags[], coverImage?, ogImage?, pinned (default false) |
| `note` | `src/content/note/**/*.{md,mdx}` | title, description?, publishDate (ISO 8601 with offset) |
| `tag` | `src/content/tag/**/*.{md,mdx}` | title?, description? |

- Title max 60 chars
- Draft posts hidden in PROD, visible in DEV
- Tags auto-lowercased and deduped

---

## File Structure
```
src/
  site.config.ts         ← central config (title, author, nav links, code theme)
  content.config.ts      ← Zod schemas for post/note/tag collections
  types.ts               ← SiteConfig, SiteMeta, Webmentions interfaces
  env.d.ts
  assets/                ← Roboto Mono fonts
  components/
    BaseHead.astro        ← meta/SEO head
    SocialList.astro      ← GitHub + LinkedIn icons
    Search.astro
    ThemeToggle.astro
    Paginator.astro
    SkipLink.astro
    ThemeProvider.astro
    FormattedDate.astro
    blog/
      Masthead.astro
      PostPreview.astro   ← post card used in lists
      TOC.astro / TOCHeading.astro
      webmentions/        ← Comments.astro, Likes.astro, index.astro
    layout/
      Header.astro        ← logo SVG + nav + search + theme toggle + mobile hamburger
      Footer.astro        ← copyright + nav links
    note/
      Note.astro
  content/
    post/
      hello-world.md           ← PLACEHOLDER — empty body, needs real content
      markdown-elements/       ← TUTORIAL post (pinned), keep or delete
        index.md
        logo.png
      testing/                 ← TUTORIAL test posts, safe to delete
        cover-image/index.md
        draft-post.md
        long-title.md
        social-image.md
      webmentions.md           ← webmentions demo post
    note/
      welcome.md               ← PLACEHOLDER intro note
    tag/
      test.md                  ← TUTORIAL tag
  data/
    post.ts                   ← getAllPosts(), getTagMeta(), groupPostsByYear(), getAllTags(), getUniqueTags(), getUniqueTagsWithCount()
  layouts/
    Base.astro
    BlogPost.astro
  pages/
    index.astro               ← shows pinned posts (max 3), latest posts (max 10), latest notes (max 5)
    about.astro               ← PLACEHOLDER — has template "I'm a starter Astro" text
    404.astro
    posts/[...page].astro
    posts/[...slug].astro
    notes/[...page].astro
    notes/[...slug].astro
    notes/rss.xml.ts
    rss.xml.ts
    tags/index.astro
    tags/[tag]/[...page].astro
    og-image/[...slug].png.ts
    og-image/_cacheUtil.ts
    og-image/_ogMarkup.ts
  plugins/
    remark-admonitions.ts
    remark-github-card.ts
    remark-reading-time.ts
  styles/
    global.css
    blocks/search.css
    components/admonition.css
    components/github-card.css
  utils/
    date.ts            ← collectionDateSort, formatted dates
    domElement.ts      ← toggleClass
    generateToc.ts
    remark.ts
    webmentions.ts
public/
  social-card.png
  icon.svg
```

---

## What Still Has Placeholder / Tutorial Content (needs replacing)
1. **`src/pages/index.astro`** — greeting says "Hi, I'm a theme for Astro..."
2. **`src/pages/about.astro`** — full template text about Astro Cactus features
3. **`src/content/post/hello-world.md`** — body is literally "Your post content here."
4. **`src/content/note/welcome.md`** — intro note about Astro Cactus features
5. **`src/content/post/testing/`** — 4 tutorial test posts (safe to delete)
6. **`src/content/post/markdown-elements/`** — markdown demo post (pinned; can keep as style reference or delete)
7. **`src/content/tag/test.md`** — test tag (can delete)
8. **`src/content/post/webmentions.md`** — webmentions demo (keep if using webmentions, else delete)

---

## Astro Cactus Theme Features
- Light/dark mode toggle (data-theme attribute, `dracula`/`github-light` code themes)
- Full-text search (pagefind)
- RSS feeds: `/rss.xml` (posts), `/notes/rss.xml` (notes)
- Auto-generated OG images per post via Satori + Roboto Mono font
- Table of Contents component for long posts
- Admonitions plugin (tip / note / important / caution / warning)
- GitHub card remark plugin
- Reading time estimate
- Webmentions support
- Paginator component
- Tags system with per-tag listing pages
- Pinned posts on homepage (max 3)
- Draft posts (hidden in production)
- Mobile-responsive hamburger nav

---

## Key Customisation Points
- **Personal info:** `src/site.config.ts` (title, author, description, url)
- **Social links:** `src/components/SocialList.astro` — already set to Kushal's GitHub + LinkedIn
- **Nav links:** `menuLinks` array in `src/site.config.ts`
- **Homepage intro:** `src/pages/index.astro` — `<h1>` and `<p>` text
- **About page:** `src/pages/about.astro` — full prose content
- **New posts:** add `.md` / `.mdx` files to `src/content/post/`
- **New notes:** add `.md` / `.mdx` files to `src/content/note/`
- **New tags:** add `.md` files to `src/content/tag/` (optional, for tag descriptions)
