# Phase 0: Content Review

**Goal**: Audit, fix, and enrich all 74 articles so they're publication-ready for a public web audience. The site is only as good as the content — this phase ensures every article is accurate, complete, well-structured, and consistent before we build anything.

**Duration estimate**: 3-5 sessions (parallel batch processing recommended)

---

## 0.1 Content Audit

### Objective
Map every article, check its current state, and flag issues.

### Steps

1. **Full inventory**: Parse all 74 articles. For each, record:
   - Title, category, word count, diagram count, code block count
   - Whether it has: intro summary, prerequisites, real-world examples, trade-offs section, "common interview questions" section, references/links
   - Broken wikilinks (`[[...]]` pointing to non-existent notes)
   - Broken external links (HTTP HEAD check)
   - Missing or placeholder sections (TODO, TBD, WIP markers)

2. **Quality scoring rubric** (1-5 per article):
   - **Completeness**: Does it cover the topic end-to-end? (back-of-envelope estimates, API design, data model, trade-offs)
   - **Accuracy**: Are the technical claims correct? (spot-check algorithms, protocols, numbers)
   - **Readability**: Is the prose clear? Are paragraphs used for concepts, bullets for lists, diagrams for architecture?
   - **Code quality**: Are Java/Python solutions correct, well-commented, and idiomatic?
   - **Interview relevance**: Does it map to common interview questions? Does it have "gotcha" moments?

3. **Output**: `content-audit.md` with a table of all 74 articles + scores + flagged issues + priority rank.

### Deliverables
- `content-audit.md` — full audit table

---

## 0.2 Content Fixes

### Objective
Fix all flagged issues from the audit. Work in priority order (lowest-scoring articles first).

### Steps

1. **Fix broken links**: Repair all broken wikilinks and external links. Either find the correct URL or remove the dead link.

2. **Fill placeholder sections**: Complete any TODO/TBD/WIP sections. No article ships with incomplete sections.

3. **Fix technical inaccuracies**: Correct any wrong claims identified during audit. Pay special attention to:
   - Capacity estimates (QPS, storage, bandwidth numbers)
   - Algorithm correctness (Big O, data structure operations)
   - Protocol details (Raft, Paxos, OAuth flows, etc.)
   - System architecture (component relationships, data flow direction)

4. **Consistency pass**: Ensure all articles follow the same structure:
   ```
   # Topic Name
   ## Overview / TL;DR
   ## Requirements (functional + non-functional)
   ## Back-of-Envelope Estimation
   ## High-Level Design
   ## Detailed Design (component-by-component)
   ## Data Model
   ## Trade-Offs / Alternative Approaches
   ## Failure Modes / Edge Cases
   ## Common Interview Questions
   ## References
   ```
   For Coding Interview Prep articles, the structure is pattern-based:
   ```
   # Pattern Name
   ## When to Use This Pattern
   ## Core Template
   ## Step-by-Step Examples (Easy → Medium → Hard)
   ## Common Variations
   ## Interview Tips / Gotchas
   ## Practice Problems
   ```

5. **Diagram audit**: For each ASCII diagram:
   - Is it readable on mobile? (long ASCII lines break on phones)
   - Should it be converted to a proper diagram for the web? (flag for Phase 2)
   - Is it labeled and referenced in the text?

### Deliverables
- Updated articles in Obsidian vault (synced to iCloud)
- `content-fixes-log.md` — log of every change made

---

## 0.3 Content Enrichment

### Objective
Make good articles great. Add the missing "why" and "so what" that turns a reference doc into a learning resource.

### Steps

1. **Add "Why This Matters" intro to every article**: 2-3 sentences explaining when/why an engineer encounters this topic in real life or interviews. Not a definition — a hook.

2. **Add real-world examples**: Every system design article should reference at least one real company's implementation (e.g., "Netflix uses this pattern for...", "Uber's dispatch system implements this as..."). Many already do — ensure all do.

3. **Add "Interview Cheat Sheet" to each article**: A compact summary box at the end:
   - 3-5 key points to remember
   - 2-3 common follow-up questions
   - 1 "gotcha" that trips people up

4. **Cross-link articles**: Create a web of connections. E.g., "Database Sharding" should link to "Consistent Hashing", "Database Indexing", "Multi-Region Active-Active". Ensure bidirectional links exist.

5. **Add difficulty + estimated read/listen time** to frontmatter:
   ```yaml
   difficulty: intermediate  # beginner | intermediate | advanced
   read_time: 25  # minutes
   listen_time: 35  # minutes (TTS is ~1.4x slower than reading)
   ```

6. **Add tags** to frontmatter for filtering:
   ```yaml
   tags: [distributed-systems, caching, redis, memcached]
   ```

### Deliverables
- Enriched articles in Obsidian vault
- `enrichment-log.md` — log of additions per article

---

## 0.4 Learning Path Organization

### Objective
Group articles into structured learning paths so the site isn't a flat list — it's a guided curriculum.

### Proposed Paths

**System Design Paths:**

1. **Foundations** (beginner): Consistent Hashing → Database Indexing → Database Sharding → Rate Limiter → URL Shortener
2. **Distributed Systems** (intermediate): Circuit Breakers → Saga Pattern → Event Sourcing & CQRS → Two-Phase Commit & Consensus → Lead Election & Gossip → Multi-Region Active-Active
3. **Data Infrastructure** (intermediate): Distributed Cache → Message Queue → Bloom Filters → Vector Database Internals → Search Engine → Data Pipeline Architecture
4. **Cloud & Infrastructure** (intermediate): CDN & Edge Computing → Kubernetes Scheduler → Service Mesh → GitOps → Chaos Engineering → Cloud Cost Optimization → Metrics & Monitoring → Distributed Tracing
5. **ML/AI Systems** (advanced): RAG Pipeline → LLM Serving at Scale → Vector Database Internals → Recommender Systems → Real-Time ML Inference → Feature Store → Fine-Tuning Infrastructure → Multi-Agent Orchestration
6. **Real-World Architecture** (intermediate): WhatsApp → Uber Dispatch → Netflix CDN → Google Search

**Coding Interview Paths:**

1. **Warm-up Patterns** (beginner): Arrays & Strings → Sliding Window → Hash Tables → Two Pointers → Sorting
2. **Tree & Graph Patterns** (intermediate): Trees → Graphs → Heaps & Priority Queues → Trie → Backtracking
3. **Advanced Patterns** (advanced): Dynamic Programming → Greedy → Bit Manipulation → Intervals → Binary Search
4. **ML Coding** (advanced): PyTorch Foundations → Transformer → ML Glossary

### Steps

1. Assign each article to 1-2 learning paths via frontmatter:
   ```yaml
   learning_path: [system-design/foundations, system-design/data-infrastructure]
   order: 3  # position within the path
   ```

2. Add "Prerequisites" and "Next Article" fields to frontmatter for sequential navigation:
   ```yaml
   prerequisites: [consistent-hashing, database-indexing]
   next: [database-sharding, multi-region-active-active]
   ```

3. Generate `learning-paths.md` with the full curriculum map.

### Deliverables
- Updated frontmatter in all articles
- `learning-paths.md` — curriculum map with article ordering

---

## 0.5 Publication Readiness Check

### Objective
Final gate before building the site. Every article must pass.

### Checklist (per article)
- [ ] No broken links (internal or external)
- [ ] No TODO/TBD/placeholder content
- [ ] Frontmatter has: title, tags, difficulty, read_time, listen_time, learning_path, order, prerequisites, next
- [ ] Structure matches the standard template (SD or CIP)
- [ ] Has "Why This Matters" intro
- [ ] Has "Interview Cheat Sheet" summary
- [ ] Has at least one real-world example/reference
- [ ] ASCII diagrams are readable (no lines > 80 chars)
- [ ] Code blocks have language annotation (```java or ```python)
- [ ] No Obsidian-specific syntax that won't render in standard markdown (except wikilinks, which Astro handles)

### Steps

1. Run automated checks (script): parse all articles, validate frontmatter, check for TODO markers, verify code block languages, check line lengths in code/diagram blocks.
2. Manual spot-check: read 5 random articles end-to-end for flow and readability.
3. Generate `publication-readiness.md` with pass/fail per article.

### Deliverables
- `publication-readiness.md` — final gate report
- All 74 articles ready for web publication

---

## Phase 0 Success Criteria

- All 74 articles pass the publication readiness check
- `content-audit.md`, `content-fixes-log.md`, `enrichment-log.md`, `learning-paths.md`, `publication-readiness.md` exist in the specs repo
- Articles are organized into learning paths with proper sequencing
- No broken links, no placeholders, no missing frontmatter
