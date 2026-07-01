# Phase 2: Polish

**Goal**: Transform a working site into a polished experience. Interactive diagrams, manual theme controls, progress tracking, playlist mode, and mobile-perfect audio. The phase where "good enough" becomes "people share this with their friends."

**Duration estimate**: 2-3 sessions

**Prerequisite**: Phase 1 complete (site is live and all MVP features work)

---

## 2.1 Interactive Diagrams

### Objective
Replace static ASCII architecture diagrams with rich, interactive SVG/Mermaid diagrams on the web — while keeping ASCII in Obsidian. The biggest visual upgrade.

### Diagram Strategy

Each article's ASCII diagrams fall into categories:

| Diagram Type | Current (ASCII) | Phase 2 (Web) |
|-------------|-----------------|---------------|
| Architecture (boxes + arrows) | ASCII art | Mermaid flowchart (themed, zoomable) |
| Data flow / sequence | ASCII steps | Mermaid sequence diagram |
| Comparison tables | Markdown tables | Styled HTML tables with hover/highlight |
| State machines | ASCII states | Mermaid state diagram |
| Data structures | ASCII trees | Custom SVG with animation |
| Capacity estimates | Text blocks | Visual calculator widgets |

### Implementation

1. **Diagram registry**: Create `src/data/diagrams.json` mapping each article to its diagrams:
   ```json
   {
     "url-shortener": [
       { "type": "mermaid", "id": "arch", "code": "graph TD; ..." },
       { "type": "mermaid", "id": "encoding", "code": "sequenceDiagram; ..." }
     ]
   }
   ```

2. **Mermaid integration**:
   - Install `mermaid` (already installed in Phase 1)
   - Create `Diagram.astro` component that renders Mermaid client-side
   - Custom Mermaid theme matching our design tokens (colors, fonts, stroke widths)
   - Pan + zoom via `svg-pan-zoom` library
   - "View as ASCII" toggle — shows the original ASCII diagram in a `<pre>` block for nostalgia/accessibility

3. **Diagram conversion process**:
   - For each of the 49 System Design articles, identify ASCII diagrams
   - Convert each to Mermaid syntax
   - Store in diagram registry
   - At build time, replace ASCII blocks in article content with rendered Mermaid diagrams
   - Keep ASCII as fallback (in `<details>` element or toggle)

4. **Animated diagrams** (select, high-impact articles only):
   - URL Shortener: animated base62 encoding flow
   - Consistent Hashing: animated ring with node add/remove
   - Rate Limiter: animated token bucket filling/draining
   - Kafka Message Queue: animated producer→partition→consumer flow
   - These use custom SVG + CSS animations, not Mermaid

### Deliverables
- `Diagram.astro` component with Mermaid rendering
- Custom Mermaid theme
- Diagram registry with all 49 SD articles' diagrams converted
- 5 animated SVG diagrams for flagship articles
- "View as ASCII" toggle on every diagram

---

## 2.2 Dark Mode Toggle

### Objective
Manual light/dark/system toggle with persistence. Phase 1 uses auto-detection only; this adds user control.

### Implementation

1. **Theme toggle component** (`ThemeToggle.astro`):
   - Three-state toggle: Light / Dark / System
   - Icon: sun (light), moon (dark), monitor (system)
   - Persists choice in `localStorage`
   - No flash of wrong theme (inline script in `<head>` reads localStorage before paint)

2. **CSS variables**: Already defined in Phase 1 for both themes. Add `[data-theme="dark"]` and `[data-theme="light"]` selectors alongside the `@media (prefers-color-scheme)` query.

3. **Inline pre-paint script** (prevents FOUC):
   ```html
   <script is:inline>
     const theme = localStorage.getItem('theme') || 'system';
     if (theme === 'dark' || (theme === 'system' && matchMedia('(prefers-color-scheme: dark)').matches)) {
       document.documentElement.setAttribute('data-theme', 'dark');
     }
   </script>
   ```

### Deliverables
- `ThemeToggle.astro` with 3-state toggle
- `localStorage` persistence
- No flash of incorrect theme on page load

---

## 2.3 Progress Tracking

### Objective
Users can track which articles they've read/listened to. motivates completion and enables "resume where you left off."

### Features

1. **Read progress per article**:
   - Track scroll position — when user reaches 90% of article, mark as "read"
   - Visual indicator on article cards (checkmark, progress ring)
   - Persist in `localStorage` (no backend, no accounts)

2. **Listen progress per article**:
   - Track audio playback position
   - Resume from last position when returning to an article
   - Visual indicator (half-filled speaker icon if partially listened)

3. **Overall progress dashboard**:
   - Progress bars per learning path: "Foundations: 3/5 articles"
   - Overall: "35/74 articles completed"
   - Streak tracker: "You've read something 5 days in a row"
   - Shown on landing page (if progress exists) or in a dedicated `/progress` page

4. **Reading history**:
   - "Recently viewed" section
   - "Continue listening" card on homepage (last article + position)

### Implementation

- All state in `localStorage` — keys: `notes:progress:<slug>` → `{ read: bool, listenProgress: 0-100, lastVisited: ISO, scrollPosition: 0-1 }`
- `useProgress()` composable/hook for reading/writing progress
- Progress sync across tabs via `storage` event

### Deliverables
- Progress tracking via `localStorage`
- Progress indicators on article cards
- Progress dashboard (learning path completion bars)
- "Continue listening" card on homepage
- Recently viewed section

---

## 2.4 Playlist Mode

### Objective
Queue articles and listen sequentially — like a real audiobook. "Play all in this learning path" or "play these articles."

### Features

1. **Add to playlist**: Button on each article card and article page. Adds to a queue.
2. **Playlist bar**: Fixed UI element showing current queue. Reorder by drag, remove items.
3. **Auto-advance**: When one article's audio finishes, automatically load and start the next.
4. **Learning path playlists**: "Play entire path" button on learning path pages. Pre-queues all articles in order.
5. **Playlist persistence**: Queue saved in `localStorage`. Survives page refresh.
6. **Shuffle/repeat**: Optional controls for variety.

### Implementation

- `PlaylistContext` (Astro client-side store using `nanostores`)
- Playlist state: `{ items: string[], currentIndex: number, isPlaying: bool, repeat: 'none'|'all'|'one', shuffle: bool }`
- When article audio ends → increment currentIndex → navigate to next article URL → auto-start audio
- Playlist bar slides up from bottom when items are queued

### Deliverables
- Playlist add/remove/reorder UI
- Auto-advance between articles
- "Play entire learning path" button
- Playlist persistence across sessions

---

## 2.5 Mobile Optimization

### Objective
Flawless mobile experience. This is the "commute listening" platform — mobile is primary, not secondary.

### Focus Areas

1. **Audio bar redesign (mobile)**:
   - Fixed bottom bar, thumb-reachable
   - Collapsed state: single play/pause button + article title (scrolling marquee if long)
   - Expanded state: full controls (speed, voice, progress, skip, stop)
   - Swipe up to expand, swipe down to collapse
   - Safe area insets (notch/Dynamic Island aware)

2. **Responsive navigation**:
   - Hamburger menu → slide-in drawer with category tree
   - Search accessible from bottom bar (thumb position)
   - Bottom tab bar (mobile only): Home | SD | CIP | Search | Playlist

3. **Content readability**:
   - 16px base on mobile (vs 18px desktop) — still larger than most sites
   - Code blocks: horizontal scroll with scroll indicator, double-tap to expand full-width
   - Tables: horizontal scroll with sticky first column
   - Diagrams: pinch-to-zoom, double-tap to fit width

4. **Performance**:
   - Lazy-load images
   - Preload audio for next article in playlist
   - Service worker for offline reading (cache article HTML)
   - View transitions for instant page changes (Astro view transitions API)

5. **Touch interactions**:
   - Swipe left/right: prev/next article in learning path
   - Long-press code block: copy
   - Pull-to-refresh on index pages

### Deliverables
- Mobile-optimized audio bar (collapsed/expanded states, swipe gestures)
- Bottom tab bar navigation
- Responsive code blocks and tables
- Service worker for offline reading
- Astro view transitions enabled
- Swipe gestures for article navigation

---

## 2.6 Article Enhancements

### Objective
Polish every article page with features that aid learning.

### Features

1. **Sticky table of contents**:
   - Auto-generated from H2/H3 headings
   - Highlights current section as you scroll (IntersectionObserver)
   - Collapsible on mobile (floating button)
   - Shows reading progress within the article (thin progress bar at top)

2. **"Was this helpful?" feedback**:
   - Thumbs up/down at article bottom
   - Optional text feedback
   - Stored in localStorage (no backend)
   - Shows aggregate: "12 people found this helpful" (if we add a simple counter via GitHub API or just hide aggregate for now)

3. **Share buttons**:
   - Copy link, X/Twitter, LinkedIn
   - "Share audio" — links to article with auto-play (?autoplay=true)

4. **Estimated times in header**:
   - "📖 25 min read · 🔊 35 min listen" badge
   - Updates live as you read/listen

5. **Related articles**:
   - "Read next" section at bottom: 3 related articles based on shared tags + learning path
   - Different from prev/next in path — these are lateral connections

6. **Keyboard shortcuts**:
   - `/` — focus search
   - `Space` — play/pause audio
   - `←` / `→` — prev/next article in path
   - `t` — toggle theme
   - `?` — show keyboard shortcuts help

### Deliverables
- Sticky TOC with scroll spy
- Feedback widget
- Share buttons
- Time estimates badge
- Related articles section
- Keyboard shortcuts help overlay

---

## 2.7 Performance & SEO

### Objective
Fast, discoverable, shareable.

### Steps

1. **Performance**:
   - Lighthouse score > 95 (all categories)
   - Image optimization via `astro:assets` (WebP, responsive sizes)
   - Font optimization: self-host Inter + JetBrains Mono, `font-display: swap`
   - Critical CSS inlined
   - Prefetch links for likely next pages (hover-based prefetch)

2. **SEO**:
   - Per-article `<title>`: "URL Shortener — System Design Notes"
   - Meta description from article's "Why This Matters" intro
   - Open Graph tags: title, description, image (auto-generated from article title + category)
   - Twitter Card tags
   - `sitemap.xml` (via `@astrojs/sitemap`)
   - `robots.txt`
   - Canonical URLs
   - Structured data (JSON-LD): Article schema with author, dateModified, keywords

3. **Social sharing**:
   - OG images: auto-generated at build time (article title + category + site name on branded background)
   - Use `satori` or `@vercel/og` to generate static OG images per article

4. **RSS feed**:
   - `@astrojs/rss` — feed of all articles
   - Separate feeds for System Design and Coding Interview
   - Useful for people who want notifications of new/updated content

### Deliverables
- Lighthouse > 95
- Full SEO meta tags + structured data
- Auto-generated OG images
- RSS feeds (all + per category)
- Sitemap + robots.txt

---

## Phase 2 Success Criteria

- All ASCII diagrams converted to interactive Mermaid/SVG (with ASCII toggle)
- Dark mode toggle with 3 states, no flash on load
- Progress tracking works (read + listen, per learning path)
- Playlist mode auto-advances through articles
- Mobile experience is flawless (audio bar, bottom tab bar, offline reading)
- Sticky TOC with scroll spy on every article
- Keyboard shortcuts work
- Lighthouse > 95
- Full SEO + OG images + RSS feeds
- 5 animated SVG diagrams on flagship articles
