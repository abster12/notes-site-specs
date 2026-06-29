# Phase 1: MVP

**Goal**: A live, narrated knowledge site on GitHub Pages. 74 articles, browsable, searchable, with a "Read Aloud" button on every page. Custom theme that looks premium — not a default template.

**Duration estimate**: 1-2 sessions

**Prerequisite**: Phase 0 complete (all articles pass publication readiness check)

---

## 1.1 Repo & Project Setup

### Objective
Create the site repo and scaffold the Astro project.

### Steps

1. **Create GitHub repo**: `abster12/notes` (public, GitHub Pages enabled)

2. **Scaffold Astro project**:
   ```
   npm create astro@latest notes -- --template minimal --typescript strict
   ```

3. **Install dependencies**:
   ```bash
   npm install @astrojs/mdx @astrojs/sitemap @astrojs/rss
   npm install tailwindcss @tailwindcss/typography
   npm install pagefind  # static search
   npm install mermaid   # diagram rendering (used in Phase 2, install now)
   ```

4. **Project structure**:
   ```
   notes/
   ├── src/
   │   ├── content/          # synced markdown articles go here
   │   │   ├── system-design/
   │   │   └── coding-interview/
   │   ├── layouts/
   │   │   ├── BaseLayout.astro       # shell: nav, footer, head
   │   │   └── ArticleLayout.astro     # article page: TOC, audio, prose
   │   ├── components/
   │   │   ├── AudioPlayer.astro       # Read Aloud button + controls
   │   │   ├── SearchBar.astro         # Pagefind integration
   │   │   ├── TableOfContents.astro   # auto-generated from headings
   │   │   ├── CodeBlock.astro         # syntax highlight + copy button
   │   │   ├── LearningPathNav.astro   # prev/next in path
   │   │   └── ArticleCard.astro       # card for index pages
   │   ├── pages/
   │   │   ├── index.astro             # landing page
   │   │   ├── system-design/index.astro
   │   │   ├── coding-interview/index.astro
   │   │   └── [...slug].astro          # dynamic article routes
   │   ├── styles/
   │   │   ├── global.css              # Tailwind + custom design tokens
   │   │   └── theme.css               # color system, typography
   │   └── utils/
   │       ├── audio.ts                # TTS logic (Web Speech API)
   │       └── content.ts              # content collection helpers
   ├── scripts/
   │   └── sync-vault.sh               # Obsidian → src/content/ sync
   ├── public/
   │   └── favicon.svg
   ├── .github/
   │   └── workflows/
   │       └── deploy.yml              # build + deploy to Pages
   ├── astro.config.mjs
   ├── tailwind.config.mjs
   └── package.json
   ```

5. **Configure Astro content collections** (`src/content/config.ts`):
   ```typescript
   import { defineCollection, z } from 'astro:content';

   const articles = defineCollection({
     type: 'content',
     schema: z.object({
       title: z.string(),
       tags: z.array(z.string()).optional(),
       difficulty: z.enum(['beginner', 'intermediate', 'advanced']).optional(),
       read_time: z.number().optional(),
       listen_time: z.number().optional(),
       learning_path: z.array(z.string()).optional(),
       order: z.number().optional(),
       prerequisites: z.array(z.string()).optional(),
       next: z.array(z.string()).optional(),
     }),
   });

   export const collections = { articles };
   ```

### Deliverables
- `abster12/notes` repo with scaffolded Astro project
- Project structure as specified
- All dependencies installed

---

## 1.2 Vault Sync Script

### Objective
Automated one-command sync from Obsidian vault to Astro content directory. Handles wikilink conversion, image paths, and frontmatter validation.

### Script: `scripts/sync-vault.sh`

**Inputs**: 
- Source: `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Hermes/`
- Target: `src/content/`

**What it does**:

1. **Copy articles**:
   - `System Design/*.md` → `src/content/system-design/`
   - `Coding Interview Prep/*.md` → `src/content/coding-interview/`
   - Skip non-article files (Resume, Job Applier, Projects, Daily Revision, Weakness Vault)

2. **Wikilink conversion**: `[[Note Name]]` → `[Note Name](/system-design/note-name)` or `[Note Name](/coding-interview/note-name)` based on which directory the target file exists in. Slugify the note name.

3. **Image handling**: Copy referenced images to `public/images/` and rewrite paths. Obsidian typically uses `![[image.png]]` — convert to standard `![alt](/images/image.png)`.

4. **Frontmatter injection**: If frontmatter is missing or incomplete, inject defaults. Ensure `title` matches the filename (minus extension).

5. **Slug generation**: Convert filenames to URL-safe slugs:
   - `Database Sharding & Replication.md` → `database-sharding-replication`
   - `URL Shortener.md` → `url-shortener`
   - Remove special chars, lowercase, hyphenate

6. **Validation**: Check that every article has required frontmatter. Exit non-zero if any article fails Phase 0 readiness check.

7. **Dry-run mode**: `./scripts/sync-vault.sh --dry-run` shows what would change without writing.

### Deliverables
- `scripts/sync-vault.sh` working end-to-end
- All 74 articles synced to `src/content/`
- Wikilinks converted to relative URLs
- Images copied and paths fixed

---

## 1.3 Custom Theme & Design System

### Objective
A premium, distinctive look — not a template. Educational, readable, with personality. Inspired by Tau's notebook aesthetic but our own identity.

### Design Tokens

```css
/* theme.css — design system foundation */

:root {
  /* Colors — warm, paper-like light theme */
  --color-bg: #faf9f6;          /* warm off-white, not pure white */
  --color-bg-card: #ffffff;
  --color-bg-code: #f5f3ef;
  --color-text: #1a1a2e;
  --color-text-secondary: #555;
  --color-accent: #e85d04;       /* warm orange (matches portfolio) */
  --color-accent-hover: #d54e00;
  --color-border: #e8e6e0;
  --color-link: #c44900;

  /* Typography */
  --font-body: 'Inter', -apple-system, sans-serif;
  --font-heading: 'Inter', -apple-system, sans-serif;
  --font-code: 'JetBrains Mono', 'Fira Code', monospace;
  --font-size-base: 18px;        /* slightly larger for readability */
  --line-height: 1.75;           /* generous for long-form reading */

  /* Spacing */
  --space-content: 720px;        /* max article width */
  --space-nav: 240px;            /* sidebar width */

  /* Transitions */
  --transition: 200ms ease;
}

@media (prefers-color-scheme: dark) {
  :root {
    --color-bg: #12121a;
    --color-bg-card: #1a1a26;
    --color-bg-code: #0d0d14;
    --color-text: #e8e8f0;
    --color-text-secondary: #999;
    --color-accent: #f77f00;
    --color-border: #2a2a3a;
    --color-link: #ffa040;
  }
}
```

### Layout Components

1. **Landing page** (`index.astro`):
   - Hero: site name + tagline ("System Design & Coding Interview Prep — Read, Browse, Listen")
   - Two main entry cards: System Design (49 articles), Coding Interview (25 articles)
   - Featured learning paths (from Phase 0)
   - "Start listening" CTA

2. **Category index pages** (`system-design/index.astro`, `coding-interview/index.astro`):
   - Grid of article cards (title, difficulty badge, read/listen time, tags)
   - Filter by tag (client-side)
   - Sort by: learning path order, difficulty, alphabetical, read time

3. **Article page** (`ArticleLayout.astro`):
   - **Left sidebar** (desktop): Table of Contents (sticky, auto-highlight current section)
   - **Main content**: article prose with styled headings, code blocks, blockquotes, tables, diagrams
   - **Top bar**: breadcrumb (Home > System Design > Article), difficulty badge, read/listen time
   - **Audio bar** (fixed bottom on mobile, floating on desktop): Read Aloud controls
   - **Bottom**: Learning path navigation (Prev / Next), tags, "Was this helpful?" feedback link

4. **Article card**: Title, 2-line excerpt, difficulty badge, time estimate, tag chips. Hover: subtle lift + accent border.

5. **Navigation bar**: Logo, System Design, Coding Interview, Search icon, dark mode toggle.

### Design Principles
- **Readability first**: 18px base font, 1.75 line-height, max 720px content width. No eye strain on long articles.
- **Personality, not minimalism**: Warm colors, subtle textures, intentional spacing. Not a sterile white docs site.
- **Mobile-first**: Audio controls accessible on phone (commute listening), content reflows properly.
- **No default Astro look**: Every component is custom-styled.

### Deliverables
- `theme.css` with full design token system
- `BaseLayout.astro`, `ArticleLayout.astro` layouts
- Landing page, category index pages, article pages all styled
- Dark/light mode via `prefers-color-scheme` (no toggle yet — Phase 2 adds manual toggle)

---

## 1.4 Read Aloud (Web Speech API)

### Objective
Every article has a "Read Aloud" button that narrates the article using the browser's built-in speech synthesis. Play/pause, stop, speed control. Zero API cost.

### Component: `AudioPlayer.astro`

**Features**:

1. **Play/Pause button**: Starts reading from the current position. Pauses and resumes.
2. **Stop button**: Stops and resets to beginning.
3. **Speed control**: 0.75x, 1x, 1.25x, 1.5x, 2x dropdown.
4. **Voice selection**: Dropdown of available system voices (populated from `speechSynthesis.getVoices()`). Prefer English voices.
5. **Progress indicator**: Visual progress bar showing % of article read. Click to seek to a section.
6. **Section tracking**: Highlight the section currently being read (syncs with TOC sidebar).
7. **Skip controls**: Skip forward/backward by paragraph or section.

**Implementation details**:

```typescript
// utils/audio.ts

interface AudioState {
  isPlaying: boolean;
  isPaused: boolean;
  currentChunk: number;
  rate: number;
  voice: SpeechSynthesisVoice | null;
}

// 1. Parse article DOM into speakable chunks
//    - Split by headings (h2, h3) into sections
//    - Within each section, split by paragraphs
//    - Skip code blocks (or read them slowly with phonetic spelling)
//    - Skip diagram captions (or read them)
//    - Handle tables: read row by row

// 2. Queue chunks via SpeechSynthesisUtterance
//    - Chain utterances: onend → speak next chunk
//    - Track current chunk index for progress + resume

// 3. Persist state per article in sessionStorage
//    - So navigating back resumes where you left off

// 4. Handle browser quirks:
//    - Chrome: 15-second utterance timeout (split long paragraphs)
//    - Safari: voices load async (wait for voiceschanged event)
//    - Firefox: limited voice selection
```

**Code block handling**: Code blocks are skipped by default. A toggle "Read code aloud" can be enabled for accessibility. When on, code is read character-by-character for short snippets, or skipped for long blocks.

**UI placement**:
- Desktop: floating bar at bottom-right (doesn't obscure content)
- Mobile: fixed bar at bottom (thumb-reachable), collapses to a single play button when scrolled

### Deliverables
- `AudioPlayer.astro` component fully functional
- `utils/audio.ts` with chunking, queuing, and state management
- Progress bar + section highlight sync
- Works in Chrome, Safari, Firefox

---

## 1.5 Search (Pagefind)

### Objective
Full-text search across all 74 articles. Instant results, no backend.

### Implementation

1. **Build step**: After Astro build, run Pagefind indexer:
   ```bash
   npx pagefind --site dist
   ```
   This indexes all HTML pages and creates a search index in `dist/pagefind/`.

2. **Search component** (`SearchBar.astro`):
   - Search input in navbar (icon on mobile, full input on desktop)
   - Results dropdown: article title, highlighted snippet, category badge
   - Keyboard navigation (arrow keys, enter to navigate)
   - `/` keyboard shortcut to focus search (like GitHub/GitLab)

3. **Search index**: Built at deploy time. Updates automatically when content changes.

### Deliverables
- Pagefind integrated into build pipeline
- `SearchBar.astro` with results dropdown
- `/` keyboard shortcut

---

## 1.6 Code Block Enhancement

### Objective
Code examples look great and are easy to use.

### Component: `CodeBlock.astro`

1. **Syntax highlighting**: Use Astro's built-in Shiki integration (VS Code themes). Choose a theme that matches our design (e.g., "github-light" for light mode, "github-dark" for dark mode).
2. **Copy button**: Top-right of each code block. Copies to clipboard, shows "Copied!" feedback.
3. **Language badge**: Shows language name (Java/Python) in top-left.
4. **Line numbers**: Optional, toggleable.
5. **Filename header**: If code block has a filename annotation, show it as a tab-like header.

### Deliverables
- `CodeBlock.astro` with Shiki highlighting, copy button, language badge

---

## 1.7 Landing Page

### Objective
A homepage that makes someone want to explore. Not just a list of links.

### Sections

1. **Hero**: 
   - Headline: "Master System Design & Coding Interviews"
   - Subheadline: "74 in-depth articles. Read them, browse them, or listen on your commute."
   - CTA buttons: "Start with System Design" → "Explore Coding Interview Prep"
   - Stats: 49 SD articles, 25 CIP articles, 74 total, ~35 hours of audio

2. **Learning Paths preview**:
   - 4-6 featured paths as horizontal cards with article count + difficulty
   - "View all paths" link

3. **Featured articles**:
   - 6 hand-picked articles (most popular/most useful)
   - Card with title, excerpt, difficulty, listen time, play button preview

4. **How it works**:
   - 3 icons: Read (browse with beautiful typography), Listen (narrated with TTS), Practice (code examples + interview tips)
   - Brief explanation of each

5. **Footer**: Links to categories, learning paths, GitHub repo, about.

### Deliverables
- `index.astro` fully designed and responsive

---

## 1.8 CI/CD & Deploy

### Objective
Push to `main` → site is live. No manual steps.

### GitHub Actions workflow: `.github/workflows/deploy.yml`

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: 20
      - run: npm ci
      - run: npm run build
      - run: npx pagefind --site dist
      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

### Steps

1. Create workflow file
2. Enable GitHub Pages in repo settings (Source: GitHub Actions)
3. Push to main, verify deploy succeeds
4. Site live at `https://abster12.github.io/notes/`

### Deliverables
- `deploy.yml` workflow file
- Site deployed and accessible
- Vault sync + build + deploy works end-to-end

---

## 1.9 Final Integration & Testing

### Objective
Everything works together. No broken pages, no missing audio, no search failures.

### Checklist

- [ ] All 74 articles render correctly (no markdown parsing errors)
- [ ] Wikilinks converted to working internal links
- [ ] Images display correctly
- [ ] Code blocks render with syntax highlighting + copy buttons
- [ ] Read Aloud works on every article (test on Chrome + Safari)
- [ ] Search returns results for common queries
- [ ] Learning path navigation (prev/next) works
- [ ] Mobile layout is correct (no horizontal scroll, audio bar reachable)
- [ ] Dark mode auto-switches based on system preference
- [ ] No console errors on any page
- [ ] Lighthouse score > 90 (performance, accessibility, best practices)
- [ ] Site loads in < 3 seconds on mobile

### Deliverables
- All checklist items pass
- Site is live and ready to share

---

## Phase 1 Success Criteria

- Site is live at `https://abster12.github.io/notes/`
- All 74 articles are browsable with custom premium theme
- Read Aloud works on every article with play/pause/stop/speed/voice
- Full-text search works
- Code blocks have syntax highlighting + copy buttons
- Learning path navigation works (prev/next within paths)
- Mobile is fully functional (audio bar, readable content, search)
- CI/CD auto-deploys on push to main
- Lighthouse score > 90
