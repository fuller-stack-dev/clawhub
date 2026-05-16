# Backend Recommendation Proof

Commit: `bfcf8bcf`
Generated: `2026-05-16T06:59:16Z`
Target: Convex dev deployment from local env; URL redacted from transcript.


## Results

- Convex functions were pushed with `bunx convex dev --once --typecheck=disable`.
- Allowed local-origin telemetry POST returned `202` with body `{"ok":true}`.
- Foreign-origin telemetry POST returned `403` with body `Search telemetry source not allowed`.
- Old `POST /api/v1/search` no longer accepted telemetry; status `404` with body `No matching routes found`.
- Endpoint write was visible in today's aggregate stats for synthetic query `browser tools pr2279-1778914756`.
- Bulk distinct-client telemetry simulation used `recordSearchInternal`, the same internal mutation called by the HTTP handler, to seed 12 curated and 12 uncurated search buckets.
- Recommendation rebuild output:

```json
{
  "count": 2,
  "ok": true
}
```

- Homepage topics after rebuild:

```json
[
  {
    "kind": "search",
    "query": "github integration",
    "reason": "Trending search",
    "score": 65
  },
  {
    "kind": "search",
    "query": "security scanner",
    "reason": "Trending search",
    "score": 60
  }
]
```

- Assertion: `security scanner` surfaced from curated search demand, while high-volume raw `cheap seo backlinks ...` did not surface.
- Retention prune first pass:

```json
{
  "dedupeDeleted": 1,
  "deleted": 2,
  "statsDeleted": 1
}
```

- Retention prune after scheduled continuation:

```json
{
  "dedupeDeleted": 0,
  "deleted": 0,
  "statsDeleted": 0
}
```

- Assertion: first prune deleted both dedupe and aggregate stat rows; scheduled continuation drained the remaining expired dedupe row before the second prune.

## Full Transcript

```text

$ bunx convex dev --once --typecheck=disable
Found multiple VITE_CONVEX_SITE_URL environment variables in .env.local so cannot update automatically.
- Preparing Convex functions...

[36mA minor update is available for Convex[39m [2m(1.38.0 → 1.39.1)[22m
[2mChangelog:[22m [4mhttps://github.com/get-convex/convex-js/blob/main/CHANGELOG.md#changelog[24m
✔ 00:59:28 Convex functions ready! (5.29s)

# HTTP endpoint checks
POST /api/v1/search/telemetry allowed local origin status: 202
Allowed response body: {"ok":true}
POST /api/v1/search/telemetry foreign origin status: 403
Blocked response body: Search telemetry source not allowed
POST /api/v1/search old read endpoint status: 404
Old path response body: No matching routes found

$ bunx convex run internal.recommendationTopics.getSearchStatsForDay {"day":20589,"limit":100}
[
  {
    "count": 13,
    "lastSearchedAt": 1778913279471,
    "query": "github integration"
  },
  {
    "count": 12,
    "lastSearchedAt": 1778913269270,
    "query": "cheap seo backlinks"
  },
  {
    "count": 1,
    "lastSearchedAt": 1778914769424,
    "query": "browser tools pr2279-1778914756"
  },
  {
    "count": 1,
    "lastSearchedAt": 1778903742144,
    "query": "agent workflow"
  },
  {
    "count": 1,
    "lastSearchedAt": 1778903376922,
    "query": "mcp tools"
  }
]

$ bunx convex run internal.searchTelemetry.pruneSearchQueryDailyDedupeInternal {"batchSize":1000}
{
  "dedupeDeleted": 0,
  "deleted": 0,
  "statsDeleted": 0
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-1"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-2"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-3"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-4"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-5"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-6"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-7"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-8"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-9"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-10"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-11"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"security scanner proof pr2279-1778914756","bucketKey":"pr2279-1778914756-curated-12"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-1"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-2"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-3"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-4"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-5"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-6"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-7"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-8"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-9"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-10"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-11"}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"cheap seo backlinks pr2279-1778914756","bucketKey":"pr2279-1778914756-uncurated-12"}
{
  "recorded": true
}

$ bunx convex run internal.recommendationTopics.rebuildHomepageRecommendationTopicsAction {"limit":8}
{
  "count": 2,
  "ok": true
}

$ bunx convex run recommendationTopics:listHomepageTopics {"limit":8}
[
  {
    "kind": "search",
    "query": "github integration",
    "reason": "Trending search",
    "score": 65
  },
  {
    "kind": "search",
    "query": "security scanner",
    "reason": "Trending search",
    "score": 60
  }
]

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"old retention proof pr2279-1778914756","bucketKey":"pr2279-1778914756-old-a","occurredAt":1776322756785}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.recordSearchInternal {"query":"old retention proof pr2279-1778914756","bucketKey":"pr2279-1778914756-old-b","occurredAt":1776322756785}
{
  "recorded": true
}

$ bunx convex run internal.searchTelemetry.pruneSearchQueryDailyDedupeInternal {"batchSize":1}
{
  "dedupeDeleted": 1,
  "deleted": 2,
  "statsDeleted": 1
}

$ bunx convex run internal.searchTelemetry.pruneSearchQueryDailyDedupeInternal {"batchSize":10}
{
  "dedupeDeleted": 0,
  "deleted": 0,
  "statsDeleted": 0
}
```
