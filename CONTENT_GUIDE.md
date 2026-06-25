# Content Authoring Guide — Astro Cactus Portfolio

Reference for writing posts, notes, and tags. Safe to delete all default tutorial content once this is saved.

---

## 1. Posts — `src/content/post/`

### Frontmatter (all fields)

```yaml
---
title: "My Post Title"              # required, max 60 chars
description: "One-line summary"     # required
publishDate: "2025-06-25"          # required — "DD Mon YYYY", ISO string, or Date
updatedDate: "2025-06-26"          # optional — shows "Updated" date on post
tags: ["tag1", "tag2"]             # optional, default [] — auto-lowercased & deduped
draft: true                        # optional, default false — hides post in PROD, visible in DEV
pinned: true                       # optional, default false — shows in "Pinned Posts" on homepage (max 3 shown)
coverImage:                        # optional — hero image at top of post
  src: "./cover.png"               #   path relative to the .md file
  alt: "Description of image"
ogImage: "/social-card.png"        # optional — custom OG image (in /public/); skips Satori auto-gen
---
```

### File location rules
- Single file post: `src/content/post/my-post-slug.md`
- Post with assets (images etc): `src/content/post/my-post-slug/index.md` + sibling files
- URL becomes: `/posts/my-post-slug/`

### Draft behaviour
- `draft: true` — only visible in dev (`pnpm dev`), hidden in prod build
- No `draft` field (or `draft: false`) — always published

---

## 2. Notes — `src/content/note/`

Shorter, informal posts. No headings usually, no cover images, no tags, no pinning.

### Frontmatter

```yaml
---
title: "Note title"                 # required, max 60 chars
description: "Optional summary"     # optional
publishDate: "2025-06-25T10:00:00Z" # required — must be ISO 8601 with timezone offset
---                                 # e.g. "2025-06-25T10:00:00Z" or "2025-06-25T12:00:00+05:30"
```

- File: `src/content/note/my-note.md`
- URL: `/notes/my-note/`
- Max 5 notes shown on homepage, all available at `/notes/`

---

## 3. Tags — `src/content/tag/` (optional)

Tags are auto-created from post frontmatter. A tag file is only needed to add a custom title/description on the tag listing page.

```yaml
---
title: "Custom Tag Title"           # optional
description: "What this tag covers" # optional
---

Optional intro paragraph shown at top of the tag page. Supports full markdown.
```

- File must be named exactly as the tag: `src/content/tag/my-tag.md`
- Tag names are always lowercased — file name must match
- If no file exists for a tag, the tag page still works, just without the custom header

---

## 4. Standard Markdown

### Headings
```md
## H2
### H3
#### H4
##### H5
###### H6
```
(H1 is the post title — do not repeat it in the body)

### Emphasis
```md
**bold**
_italic_
~~strikethrough~~
```

### Blockquotes
```md
> Single level
>
> > Nested
```

### Lists
```md
- Unordered item
  - Nested (2 spaces indent)

1. Ordered item
2. Second item
```

### Links
```md
[Link text](https://example.com)
[Internal link](/posts/my-post/)
```

### Images
```md
![Alt text](./image.png)       # relative to the .md file (put image in same folder)
![Alt text](/public-image.png) # from /public/
```

### Footnotes
```md
Some text with a reference[^1].

[^1]: Footnote content here.
```

### Tables
```md
| Column 1 | Column 2 | Column 3  |
| -------- | :------: | --------: |
| left     | center   |     right |
```

### Inline code
```md
`inline code`
```

---

## 5. Expressive Code Blocks (enhanced fenced code)

### Basic with language
````md
```js
const x = 1;
```
````

### With filename title
````md
```js title="utils.js"
export const add = (a, b) => a + b;
```
````

### Bash terminal
````md
```bash
npm install my-package
```
````

### Line markers
````md
```js title="example.js" del={2} ins={3-4} {6}
function demo() {
  console.log("deleted line");     // red background
  // inserted line 1               // green background
  console.log("inserted line 2"); // green background

  return "neutral highlight";      // neutral highlight (line 6)
}
```
````

- `del={N}` — mark line N as deleted (red)
- `ins={N}` or `ins={N-M}` — mark line(s) as inserted (green)
- `{N}` — neutral highlight
- Ranges: `del={2-4}`, `ins={6-8}`

---

## 6. Admonitions

Wrap content in `:::type` ... `:::`. Rendered as styled callout boxes.

### Types: `note` · `tip` · `important` · `caution` · `warning`

```md
:::note
Highlights information users should take into account, even when skimming.
:::

:::tip
Optional information to help a user be more successful.
:::

:::important
Crucial information necessary for users to succeed.
:::

:::caution
Negative potential consequences of an action.
:::

:::warning
Critical content demanding immediate user attention due to potential risks.
:::
```

### Custom title
```md
:::note[My Custom Title]
This note has a custom heading instead of "Note".
:::
```

---

## 7. GitHub Cards

Renders a dynamic card fetched from the GitHub API at page load.

```md
::github{repo="owner/repo-name"}

::github{user="github-username"}
```

Examples:
```md
::github{repo="Kushalgouda-Patil/some-repo"}
::github{user="Kushalgouda-Patil"}
```

---

## 8. OG / Social Images

**Auto-generated (default):** Satori generates a PNG from post title + author using Roboto Mono. No action needed.

**Custom OG image:** Place image in `/public/` and add to frontmatter:
```yaml
ogImage: "/my-custom-og.png"
```
This skips Satori and uses the specified image instead.

**Cover image (hero at top of post):** Place image next to the `.md` file and reference it:
```yaml
coverImage:
  src: "./cover.png"
  alt: "Description of the image"
```

---

## 9. Webmentions (optional feature)

To enable webmentions (show likes/comments/replies from social media on posts):

1. Add `isWebmention: true` to a social link in `src/components/SocialList.astro` — adds `rel="me authn"`
2. Create account at webmention.io using your domain
3. Add to `.env` (rename `.example.env`):
   ```
   WEBMENTION_URL=https://webmention.io/api/mentions.jf2?target=
   WEBMENTION_API_KEY=your-key-here
   WEBMENTION_PINGBACK=https://webmention.io/your-domain/xmlrpc  # optional
   ```
4. Connect social accounts at brid.gy
5. Rebuild/redeploy — webmentions appear at the bottom of posts

Note: `.app` TLDs do not work with webmention.io or brid.gy.

---

## 10. RSS Feeds

Auto-generated — no action needed:
- `/rss.xml` — all posts
- `/notes/rss.xml` — all notes

---

## 11. Quick-Delete Checklist

Safe to delete now that this guide is saved:

- [ ] `src/content/post/hello-world.md`
- [ ] `src/content/post/markdown-elements/` (whole folder)
- [ ] `src/content/post/testing/` (whole folder)
- [ ] `src/content/post/webmentions.md` (unless you plan to set up webmentions)
- [ ] `src/content/note/welcome.md`
- [ ] `src/content/tag/test.md`
