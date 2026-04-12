# Performance Checklist Reference

Lookup checklist for performance review. Referenced by skills — not a skill itself.

---

## Measurement First

Never optimize without measurement. Profile, identify the bottleneck, fix it, measure again.

### Core Web Vitals

| Metric | Good | Needs Work | Poor |
|--------|------|-----------|------|
| LCP (Largest Contentful Paint) | <=2.5s | <=4.0s | >4.0s |
| INP (Interaction to Next Paint) | <=200ms | <=500ms | >500ms |
| CLS (Cumulative Layout Shift) | <=0.1 | <=0.25 | >0.25 |

### Measurement Commands

```bash
# Lighthouse (browser)
npx lighthouse https://url --output=json --output-path=report.json

# Bundle analysis
npx webpack-bundle-analyzer stats.json
npx source-map-explorer build/static/js/*.js

# Node.js profiling
node --prof app.js
node --prof-process isolate-*.log > profile.txt

# Database query analysis
EXPLAIN ANALYZE SELECT ...;
```

## Frontend Performance

- [ ] Images: modern formats (WebP/AVIF), responsive sizes, lazy loading below fold
- [ ] Fonts: `font-display: swap`, preload critical fonts, subset to used characters
- [ ] JavaScript: code-split by route, tree-shake unused exports, defer non-critical scripts
- [ ] CSS: purge unused styles, inline critical CSS, load rest async
- [ ] Caching: immutable assets with content hashes, appropriate Cache-Control headers
- [ ] Layout: dimensions on images/video to prevent CLS, avoid dynamic injection above fold

## Backend Performance

- [ ] Database: indexes on queried columns, avoid N+1 queries, use connection pooling
- [ ] Caching: cache expensive computations, cache external API responses, invalidation strategy
- [ ] Queries: select only needed columns, paginate large results, avoid full table scans
- [ ] APIs: support pagination, compress responses (gzip/brotli), set appropriate timeouts
- [ ] Async: offload slow operations to background jobs, use queues for heavy processing
- [ ] Memory: watch for leaks in long-running processes, profile allocation patterns

## When to Optimize

1. **Always:** algorithmic complexity (O(n^2) -> O(n log n))
2. **Always:** obvious waste (loading unused data, redundant queries)
3. **Measure first:** micro-optimizations, caching, lazy loading
4. **Never:** premature optimization without measured bottleneck
