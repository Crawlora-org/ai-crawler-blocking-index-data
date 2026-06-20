# AI-Crawler Blocking Index

A `robots.txt` census of the **Tranco top 1,000,000 domains** (June 2026): which AI crawlers does each
site block? Every domain's `/robots.txt` is fetched and parsed for 20 AI-crawler user-agents (GPTBot,
CCBot, ClaudeBot, Google-Extended, Bytespider, …) and whether each is `Disallow: /`'d. This is the
dataset behind the Crawlora **AI-Crawler Blocking Index**. CC BY 4.0.

## Headline
- **998,497** domains scanned · **629,565 (63.1%)** serve a `robots.txt`.
- **9.33%** fully block at least one major AI crawler (a `User-agent: X` group with `Disallow: /`) —
  **14.8%** of the robots-serving sites.
- Most-blocked: **GPTBot 7.4% ≈ CCBot 7.2%** — the open Common Crawl corpus is blocked nearly as hard
  as OpenAI's own crawler — then Bytespider 6.8%, ClaudeBot 6.7%, Amazonbot 6.6%, Google-Extended 6.3%.
- **Concentrated at the head:** ~13% of the top 10,000 block an AI crawler, fading to ~9% across the
  long tail — the *inverse* of managed anti-bot / WAF adoption, which climbs down the ranking.
- **7.19%** block every bot with a blanket `User-agent: * / Disallow: /` — overwhelmingly the parked /
  abandoned tail.
- The big training crawlers are blocked ~6× more than the AI-search / assistant agents that return
  traffic (PerplexityBot, ChatGPT-User, OAI-SearchBot ≈ 1%): sites distinguish "train on me" from
  "send me users".

## What "fully blocks" means
A site fully blocks a crawler when its `robots.txt` **names that user-agent** (exact, case-insensitive)
in a group with **`Disallow: /`**. That's the strict, unambiguous "kept off the whole site" signal — it
excludes partial-path disallows (`Disallow: /private`) and allow-only mentions, so these numbers are
lower than studies that count any mention of a bot. `robots.txt` is a published *request*, not an
enforced wall — a crawler can ignore it. This measures stated policy, not traffic.

## Files
- `data/results.jsonl.gz` — one JSON record per domain (gzipped), 998,497 rows.
- `data/sample.jsonl` — first 200 records, uncompressed preview.
- `data/summary.json` — headline aggregates: per-crawler blocking, the rank gradient, totals.

Read it: `gzip -dc data/results.jsonl.gz | head`, or in Python
`for line in gzip.open("data/results.jsonl.gz","rt"): rec = json.loads(line)`.

## Schema (one JSON record per line)
`domain`, `rank` (Tranco), `scheme` (https/http), `robots_status` (HTTP status of `/robots.txt`),
`has_robots` (200 + body), `names_ai` (AI user-agents named in robots.txt), `blocks_ai` (AI user-agents
with `Disallow: /`), `blocks_any_ai`, `star_disallow_root` (a `User-agent: * / Disallow: /` group).

## Methodology
One `GET /robots.txt` per domain (https first, http fallback) from a datacenter IP, parsed into
`User-agent` groups. 20 AI user-agents tracked across OpenAI, Anthropic, Google, Common Crawl, ByteDance,
Meta, Amazon, Apple, Perplexity, Cohere and others. **Same domain universe as the
[Anti-Bot Adoption Index](https://github.com/Crawlora-org/anti-bot-adoption-index-data)** — the two
datasets join on `domain`, so you can ask "does a managed-WAF site also block AI in robots.txt?".

## License & sources
**CC BY 4.0.** Interactive explorer: https://crawlora.net/ai-crawler-index · companion studies:
https://crawlora.net/anti-bot-index , https://crawlora.net/dead-web-index
