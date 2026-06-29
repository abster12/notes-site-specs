# Phase 3: Premium

**Goal**: Studio-quality narration, runnable code, custom domain, and community features. The phase that turns a personal study site into a resource others rely on daily.

**Duration estimate**: 3-5 sessions

**Prerequisite**: Phase 2 complete (polished site with all interactive features)

**Cost note**: This phase introduces optional paid components (TTS API, custom domain). All costs are clearly marked. Core site remains free.

---

## 3.1 Neural TTS Audio Generation

### Objective
Generate studio-quality audio narration for every article at build time. Browser TTS (Phase 1) is the free fallback; this is the premium tier.

### Provider Selection

| Provider | Quality | Cost (per 1M chars) | Voice Style | Notes |
|----------|---------|---------------------|-------------|-------|
| OpenAI TTS (tts-1-hd) | Excellent | $15 | 6 voices, natural | Simple API, great quality |
| ElevenLabs | Studio | $22+ (varies by plan) | Voice cloning, 100+ voices | Best quality, most flexible |
| MiniMax | Very Good | ~$8 | Multilingual | Cost-effective |
| Google Cloud TTS | Good | $16 (WaveNet), $160 (Neural2) | 380+ voices, 50+ languages | Enterprise-grade |

**Recommendation**: Start with OpenAI TTS (tts-1-hd) — best quality-to-simplicity ratio. Upgrade to ElevenLabs if voice quality becomes a priority.

### Implementation

1. **Build-time generation script** (`scripts/generate-audio.ts`):
   ```typescript
   // For each article:
   // 1. Extract plain text from markdown (strip code blocks, diagrams, frontmatter)
   // 2. Clean text for TTS (expand abbreviations, handle numbers, add pauses)
   // 3. Split into chunks under API limit (OpenAI: 4096 chars)
   // 4. Call TTS API for each chunk
   // 5. Concatenate audio chunks into single MP3
   // 6. Save to public/audio/<slug>.mp3
   // 7. Record metadata (duration, file size, voice, generated date) in frontmatter
   ```

2. **Text preprocessing for TTS**:
   - Remove markdown syntax (headers, bold, links, images)
   - Skip code blocks (or read them slowly with natural language descriptions)
   - Expand abbreviations: "e.g." → "for example", "i.e." → "that is", "API" → "A-P-I"
   - Handle numbers: "1M" → "one million", "100ms" → "one hundred milliseconds"
   - Add natural pauses at section boundaries
   - Clean up table data (read as "Column: value, Column: value")

3. **Audio player upgrade**:
   - If neural audio exists for an article → use `<audio>` element with the MP3
   - If not → fall back to Web Speech API (Phase 1 behavior)
   - Toggle: "Neural voice" / "Browser voice" in audio controls
   - Neural audio: seekable, scrubbable progress bar, offline-capable (service worker caches audio)

4. **Incremental generation**:
   - Check if article content hash changed since last generation
   - Only regenerate audio for changed articles
   - `scripts/generate-audio.ts --force` to regenerate all

5. **Cost management**:
   - Total text across 74 articles: ~2.8MB ≈ ~2.8M characters
   - OpenAI TTS cost: ~$42 for full library (one-time)
   - Incremental updates: ~$0.01-0.05 per changed article
   - Cache generated audio in repo (Git LFS if needed) or in GitHub release assets

6. **Voice selection**:
   - Default: "nova" (OpenAI) — warm, clear, educational tone
   - Alternative: "onyx" (deeper, authoritative) or "shimmer" (brighter)
   - User can switch voices; audio is regenerated or pre-generated per voice (optional)

### Deliverables
- `scripts/generate-audio.ts` — TTS generation pipeline
- Audio MP3s for all 74 articles
- Audio player upgraded to use neural audio with browser fallback
- Incremental generation (only changed articles)
- Cost: ~$42 one-time (OpenAI TTS)

---

## 3.2 Runnable Code Examples

### Objective
Code examples aren't just readable — they're runnable. Right in the browser. No setup, no IDE.

### Approach: WebAssembly-based execution

1. **Python**: Pyodide (CPython compiled to WASM)
   - Runs in browser, no server
   - Supports standard library + numpy, pandas (for ML coding examples)
   - ~10MB initial download (cached by service worker after first load)

2. **Java**: CheerpJ (OpenJDK compiled to WASM) OR Judge0 API
   - CheerpJ: fully client-side, ~30MB initial download (heavy but cached)
   - Judge0: free tier (50 req/day), runs on remote server
   - **Recommendation**: Start with Judge0 for Java (smaller payload), Pyodide for Python (fully client-side)

### Implementation

1. **Interactive code block** (`RunnableCode.astro`):
   ```
   ┌──────────────────────────────────────┐
   │ .java                    [Run] [Reset]│
   │ ┌──────────────────────────────────┐ │
   │ │ // Example: Binary search        │ │
   │ │ int search(int[] nums, int t) {  │ │
   │ │   int lo = 0, hi = nums.length-1;│ │
   │ │   ...                            │ │
   │ │ }                                │ │
   │ └──────────────────────────────────┘ │
   │ ┌──────────────────────────────────┐ │
   │ │ Output:                           │ │
   │ │ > Found at index: 4               │ │
   │ └──────────────────────────────────┘ │
   └──────────────────────────────────────┘
   ```

2. **Editor**: Monaco Editor (VS Code's editor) or CodeMirror 6
   - Syntax highlighting
   - Basic autocompletion
   - Line numbers
   - Multiple themes (match site theme)

3. **Execution flow**:
   - User clicks "Run"
   - Code sent to execution engine (Pyodide in Web Worker / Judge0 API)
   - Output streamed to output panel
   - Timeout: 5 seconds (prevent infinite loops)
   - Error messages rendered with line numbers

4. **Which examples are runnable?**:
   - Coding Interview Prep: all pattern templates + examples (Java + Python)
   - System Design: pseudo-code snippets and algorithm implementations
   - Not every code block — just the ones where running adds value (flagged in frontmatter: `runnable: true`)

5. **Share output**: Users can share a link to a code example with their modifications pre-loaded (encode code in URL hash or use a simple gist-like storage).

### Deliverables
- `RunnableCode.astro` component with Monaco/CodeMirror editor
- Python execution via Pyodide (client-side)
- Java execution via Judge0 API (or CheerpJ if preferred)
- "Run" button on all flagged code examples
- Output panel with streaming + error handling
- 5-second timeout

---

## 3.3 Custom Domain & Branding

### Objective
Professional domain, consistent brand identity.

### Domain Options
- `abhigyan.dev/notes` (if abhigyan.dev is available)
- `notes.abhigyan.dev` (subdomain)
- `systemdesign-notes.com` (descriptive, SEO-friendly)
- `interviewkit.dev` (branded, memorable)
- Keep `abster12.github.io/notes` (free, no domain cost)

### Steps

1. **Domain setup** (if custom domain chosen):
   - Purchase domain (~$12/year)
   - Configure DNS: A record → GitHub Pages IPs, CNAME → `abster12.github.io`
   - Add `CNAME` file to repo
   - Enable HTTPS via GitHub Pages (automatic Let's Encrypt)

2. **Brand identity**:
   - Logo: simple, memorable mark (derived from the τ/notes concept)
   - Favicon: SVG, works at all sizes
   - Social preview image: branded OG image template
   - Consistent color palette across all touchpoints

3. **About page** (`/about`):
   - Who made this and why
   - Content philosophy (paragraphs for concepts, bullets for lists, diagrams for architecture)
   - How to use the site (read, listen, practice)
   - GitHub link, contact

### Deliverables
- Custom domain configured (or decision to keep GitHub Pages URL)
- Logo + favicon + brand assets
- About page

---

## 3.4 Analytics & Insights

### Objective
Understand how people use the site — which articles are popular, where they drop off, what they search for.

### Approach: Privacy-first, no cookies, no tracking

1. **Analytics platform**: Plausible (open-source, privacy-friendly) or Umami
   - Self-hosted Umami: free, runs on a small VPS or on Vercel
   - Plausible Cloud: $9/month, zero setup
   - **Recommendation**: Umami self-hosted (free, privacy-first)

2. **Metrics tracked**:
   - Page views per article
   - Time on page (engagement)
   - Audio play rate + completion rate
   - Search queries (what people look for)
   - Learning path progression (how far people get in each path)
   - Referrers (where traffic comes from)
   - Country/language (anonymized, no PII)

3. **Content insights dashboard** (`/admin` or local-only):
   - Most-read articles
   - Most-listened articles
   - Articles with high bounce rate (may need improvement)
   - Search queries with no results (content gaps)
   - Learning path completion funnel

4. **No cookies, no GDPR popup needed**:
   - Umami/Plausible use anonymized, aggregate data
   - No personal data collected
   - Cookie banner not required

### Deliverables
- Umami (or Plausible) integrated
- Analytics dashboard with content insights
- Privacy policy page

---

## 3.5 Community Features

### Objective
Let others benefit from and contribute to the content. Turn a solo study site into a shared resource.

### Features

1. **Comments / discussions per article**:
   - **Option A**: Giscus (GitHub Discussions-backed) — free, requires GitHub account, high-quality discussions
   - **Option B**: Disqus — free, broader audience, but ads on free tier
   - **Recommendation**: Giscus — developer audience already has GitHub, discussions are searchable and moderated via GitHub
   - Placement: bottom of each article, above "Related articles"
   - Theme: matches site theme (light/dark)

2. **Article reactions**:
   - Emoji reactions (👍, 🤯, 🧠, 🤔) via Giscus or custom implementation
   - Shows aggregate count (social proof)

3. **"Suggest improvement" link**:
   - Each article has "Edit on GitHub" link → opens the source markdown in the repo
   - Users can submit PRs to fix/improve content
   - CONTRIBUTING.md with guidelines

4. **Newsletter / RSS subscriptions**:
   - RSS feed (already from Phase 2)
   - Optional: Buttondown email newsletter for "new articles" notifications (free up to 100 subscribers)

5. **Share progress**:
   - "I completed the System Design Foundations path!" → shareable image/card
   - Generate a branded completion certificate (PDF or image) per learning path

### Deliverables
- Giscus comments on every article
- Emoji reactions
- "Edit on GitHub" link + CONTRIBUTING.md
- Optional newsletter integration
- Shareable completion certificates

---

## 3.6 Content Expansion Pipeline

### Objective
Make it easy to add new content. The site should grow without friction.

### Steps

1. **New article template**:
   - `scripts/new-article.sh <title> --category <system-design|coding-interview>`
   - Creates a new markdown file with the standard template + frontmatter
   - Opens in Obsidian (via URL scheme: `obsidian://open?vault=Hermes&file=...`)

2. **Auto-categorization**:
   - When a new article is added to the vault, the sync script detects it
   - Prompts for category, tags, learning path assignment
   - Or auto-suggests based on content (keyword matching)

3. **Audio auto-generation**:
   - When a new/updated article is pushed, GitHub Action runs TTS generation
   - Audio is committed to the repo (or uploaded as release asset)
   - No manual audio work needed

4. **Content calendar**:
   - Track which articles are planned, in-progress, ready
   - Simple `content-pipeline.md` in the repo with a Kanban-style table

### Deliverables
- `scripts/new-article.sh` template generator
- Auto-detection of new articles in sync script
- GitHub Action for auto-generating audio on new content
- Content pipeline tracker

---

## 3.7 Advanced Audio Features

### Objective
Make the audio experience as good as a real audiobook or podcast.

### Features

1. **Background play**:
   - Media Session API integration — audio controls show in phone lock screen, notification shade, Bluetooth headphones
   - Play/pause/skip from lock screen
   - Album art: article title + category on branded background

2. **Sleep timer**:
   - "Stop playing after: 15min / 30min / 45min / 1hr / End of article"
   - Fades out volume in last 10 seconds

3. **Bookmarks**:
   - "Bookmark this moment" — saves audio position + timestamp
   - Bookmarks list per article
   - Click bookmark to jump to that position

4. **Speed control (enhanced)**:
   - Granular: 0.5x to 3x in 0.25 increments
   - "Trim silence" option (removes pauses for faster listening)
   - Volume boost (for quiet audio)
   - Pitch preservation (speed up without chipmunk voice) — if using `<audio>` element with `preservesPitch`

5. **Download for offline**:
   - "Download audio" button — saves MP3 to device
   - Service worker caches audio for offline playback
   - "Download entire learning path" — zips all audio files for a path

6. **Transcript view**:
   - Toggle: show transcript alongside audio
   - Transcript highlights in sync with audio playback
   - Click transcript text to seek to that point
   - Useful for deaf/hard-of-hearing users and for searching within audio

### Deliverables
- Media Session API integration (lock screen controls)
- Sleep timer
- Audio bookmarks
- Enhanced speed control + trim silence
- Download for offline
- Transcript view synced with audio

---

## Phase 3 Success Criteria

- Neural TTS audio available for all 74 articles (with browser TTS fallback)
- Code examples are runnable in-browser (Python via Pyodide, Java via Judge0)
- Custom domain (or deliberate decision to keep GitHub Pages URL)
- Analytics dashboard with content insights (Umami/Plausible)
- Giscus comments + emoji reactions on every article
- "Edit on GitHub" + CONTRIBUTING.md for community contributions
- Background play via Media Session API
- Sleep timer, bookmarks, download for offline
- Transcript view synced with audio
- Content expansion pipeline (template, auto-audio, pipeline tracker)
- Total cost: ~$42 (one-time TTS) + optional $12/year (domain) + $0 (everything else)
