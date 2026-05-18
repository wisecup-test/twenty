<rule_activation id="fa6e6daa-de24-425a-bd31-be858bfae48d" title="Implement Cache Layer for External API Relationship Data: Cache Entries Include" applies_to="**/*">
These rules are ALWAYS ACTIVE for all external API integrations that expose relational data models, frontend components that render or manipulate relationship data, API client libraries, and data persistence hooks for relational API data.
</rule_activation>

### Rules

- **R-CACHE-001** SHOULD: Cache entries SHOULD include TTL (time-to-live) configurations appropriate to the data volatility and consistency requirements.

### Verify

```bash
# Detect cache and relationship patterns in codebase
grep -r 'cache.*relationship\|relationship.*cache' --include='*.ts' --include='*.tsx' packages/

# Search for specific cache layer implementations
grep -r 'useMorphPersist\|cache_layer\|cacheRelation' --include='*.ts' --include='*.tsx' packages/

# Find cache-related files with relationship handling
find . -type f -name '*cache*.ts' -o -name '*Cache*.ts' | xargs grep -l 'relationship\|relation'
```

**Accept when:**
- Cache layer implementation is detected in API integration modules with relationship handling logic
- Cache invalidation logic is present alongside relationship persistence operations
- Monitoring or metrics collection for cache performance is implemented or documented
- TTL configurations are defined for cached relationship data
- Cache key strategies incorporate entity IDs and relationship types

<enforcement>
Claude Code MUST verify cache layer implementation for all external API integrations exposing relational data. Verification is mandatory before accepting new API integration code. Performance testing must validate cache hit rates meet defined thresholds (>70% for stable relationship data). Violations require written justification in architecture review.
</enforcement>