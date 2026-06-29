# Notes — Knowledge Audiobook Site

A premium educational knowledge site that transforms Obsidian notes into a browsable, narrated learning experience. Built around Abhigyan's System Design (49 articles) and Coding Interview Prep (25 articles) vaults.

## Vision

Not just a blog. A full "learning experience" — every article is readable, browsable, AND narrated as audio. Think: Tau (twotimespi.dev) level of polish, but with 74 deep articles as the engine instead of a single landing page. Free to host, free to use, genuinely useful for interview prep and system design learning.

## What Makes It Better

| Dimension | Tau (twotimespi.dev) | Notes (ours) |
|-----------|---------------------|--------------|
| Content depth | Single project landing page | 74 articles, 20-74KB each, real interview prep material |
| Audio | None | Per-article narration (browser TTS + optional neural TTS) |
| Navigation | Flat single page | Full-text search, learning paths, tag filtering, progress tracking |
| Code examples | Minimal | Java + Python with syntax highlighting, copy buttons, optionally runnable |
| Diagrams | Animated unit circle | Interactive Mermaid/SVG for architecture, ASCII preserved in Obsidian |
| Audience | Devs learning agent internals | Engineers prepping for system design + coding interviews |

## Content Inventory

### System Design (49 articles)
- **Core Systems**: URL Shortener, Rate Limiter, Chat System, Twitter Feed, Notification System, Payment System, Search Engine
- **Data Infrastructure**: Database Indexing, Database Sharding, Distributed Cache, Consistent Hashing, Message Queue, Bloom Filters, Vector Database Internals
- **Distributed Systems**: Circuit Breakers, Saga Pattern, Event Sourcing & CQRS, Two-Phase Commit & Consensus, Lead Election & Gossip, Multi-Region Active-Active, Geo-Distributed Databases
- **ML/AI**: RAG Pipeline, LLM Serving at Scale, Multi-Agent Orchestration, Recommender Systems, Real-Time ML Inference, Feature Store, Fine-Tuning Infrastructure, Vector Database Internals
- **Cloud/Infra**: CDN & Edge Computing, Kubernetes Scheduler, Service Mesh, GitOps, Chaos Engineering, Cloud Cost Optimization, Metrics & Monitoring, Distributed Tracing, S3-like Object Storage, Data Pipeline Architecture
- **Real-World**: WhatsApp Architecture, Uber Dispatch, Netflix CDN, Google Search
- **Security/Auth**: Authentication System, Privacy & Compliance
- **Advanced**: Collaborative Editor (CRDTs), Video/Image Storage, Rate Limiting Algorithms Deep Dive, Rate Limiter LLD, Design an Idempotent API

### Coding Interview Prep (25 articles)
- **Patterns**: Arrays & Strings, Sliding Window, Two Pointers, Intervals, Binary Search, Hash Tables, Stacks & Queues, Linked Lists, Trees, Graphs, Heaps & Priority Queues, Trie, Dynamic Programming, Greedy, Backtracking, Sorting, Bit Manipulation
- **Foundations**: Big O & Pattern Recognition, Python Internals
- **ML Coding**: PyTorch Foundations, Transformer, ML Glossary
- **Interview Strategy**: Interview Process & Strategy, Project Q&A and Behavioral Stories, Design Problems
- **Company-Specific**: Hedgineer Applied AI Engineer prep

## Phases

| Phase | Name | Status | Description |
|-------|------|--------|-------------|
| 0 | Content Review | Not Started | Audit, fix, enrich all 74 articles for web publication |
| 1 | MVP | Not Started | Astro site, vault sync, custom theme, Read Aloud, basic search — live on GitHub Pages |
| 2 | Polish | Not Started | Interactive diagrams, dark mode, progress tracking, playlist mode, mobile optimization |
| 3 | Premium | Not Started | Neural TTS audio, runnable code, custom domain, analytics, community features |

See `phase-0-content-review.md`, `phase-1-mvp.md`, `phase-2-polish.md`, `phase-3-premium.md` for detailed specs.

## Tech Stack (Summary)

- **Framework**: Astro (content-first, MDX, partial hydration)
- **Content source**: Obsidian vault (iCloud Hermes vault)
- **Styling**: Tailwind CSS + custom design system
- **Audio**: Web Speech API (Tier 1) / OpenAI TTS or ElevenLabs (Tier 2)
- **Diagrams**: Mermaid.js + custom SVG animations
- **Search**: Pagefind (static site full-text search)
- **Deploy**: GitHub Actions → GitHub Pages
- **Cost**: $0 for Phase 0-2, optional ~$5-15/mo for Phase 3 TTS API

## Vault Location

- **Primary**: `~/Library/Mobile Documents/iCloud~md~obsidian/Documents/Hermes/`
  - `System Design/` — 49 articles
  - `Coding Interview Prep/` — 25 articles
- **Secondary (older)**: `~/Obsidian Vault/` (not included in initial scope)

## Repo

- **Specs repo**: This repo (`abster12/notes-site-specs`)
- **Site repo (Phase 1)**: `abster12/notes` (to be created in Phase 1)
