# agentic-digests

Weekly AI research digests generated autonomously every Monday and archived here. Each digest is the exact markdown sent to subscribers that week.

## Digests

| Folder | Topic | Window |
|--------|-------|--------|
| `ai-ecosystem/` | Claude Code, MCP servers, AI developer tooling | Last 7 days |
| `ai-marketing/` | AI marketing tools, video, growth automation | Last 14 days |
| `dtc-marketing/` | DTC ecommerce, paid social, retention & LTV | Last 30 days |

---

## How it works

Three GitHub Actions jobs fire every Monday at **6:30am EDT (10:30am UTC)** from [`louloret/last30days-skill`](https://github.com/louloret/last30days-skill), branch `custom`.

### Pipeline (per digest)

```
1. Fetch subscriber list
         ↓
2. Research: last30days.py
   Reddit · X · HackerNews
         ↓
3. Synthesize: Claude Haiku
   narrative + GitHub repo sections
         ↓
4. Deliver: Resend → subscribers
         ↓
5. Archive: markdown saved here
```

### Research engine

Each digest runs [`scripts/last30days.py`](https://github.com/mvanhorn/last30days-skill) with a hardcoded plan — a set of weighted subqueries targeting specific topics. The engine searches across three sources in parallel:

| Source | How it searches | Weight |
|--------|----------------|--------|
| Reddit | Semantic full-text across topic-specific subreddits | 2.5 (highest signal) |
| HackerNews | Claude-generated Algolia query (1–3 keywords) | 1.0 (baseline) |
| X | Claude-generated OR phrase query, `min_faves:3` | 0.7 (de-weighted for hype) |

Claude Haiku generates the X and HN search queries at runtime based on each subquery's intent, so searches adapt to what's actually relevant that week rather than matching a fixed keyword list.

### Synthesis

Raw research output (scored, deduplicated posts) is piped through `digest/email_digest.py`. Claude Haiku reads the compact research and writes a narrative digest covering:

- Top themes and signals from the week
- Trending GitHub repos (new repos gaining stars fast)
- Top all-time repos by topic (ranked by `log10(stars) × recency` blend)

---

## Source repos

- **Research engine**: [`mvanhorn/last30days-skill`](https://github.com/mvanhorn/last30days-skill) — public upstream
- **Digest workflows**: [`louloret/last30days-skill`](https://github.com/louloret/last30days-skill) — fork running the scheduled jobs (branch `custom`)
