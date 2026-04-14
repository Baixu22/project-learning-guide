# Execution Trace and Observability

Track skill execution for debugging, performance analysis, and quality assurance.

## Trace Structure

Each skill execution generates a trace record:

```yaml
execution_trace:
  run_id: "20260414-001"  # timestamp + sequence
  timestamp: "2026-04-14T10:00:00Z"
  skill_name: "deep_analysis"
  skill_category: "composite"
  
  # Input parameters
  input:
    target_module: "AuthService"
    analysis_mode: "moderate_analysis"
    user_overrides:
      detail_level: "intermediate"
  
  # Sub-skill executions (for composite skills)
  sub_skills:
    - skill: "discover_project"
      start_time: "2026-04-14T10:00:01Z"
      end_time: "2026-04-14T10:00:03Z"
      duration_ms: 2000
      status: "success"
      tool_calls: 8
      tokens_used: 3500
      output_summary: "project_type: frontend-react, 87 files"
    
    - skill: "analyze_symbol"
      start_time: "2026-04-14T10:00:03Z"
      end_time: "2026-04-14T10:00:11Z"
      duration_ms: 8000
      status: "success"
      tool_calls: 15
      tokens_used: 12000
      output_summary: "AuthService: 3 patterns, confidence HIGH"
    
    - skill: "write_module_doc"
      start_time: "2026-04-14T10:00:11Z"
      end_time: "2026-04-14T10:00:17Z"
      duration_ms: 6000
      status: "success"
      tool_calls: 2
      tokens_used: 8000
      output_summary: "docs/modules/03-auth.md created"
  
  # Aggregated metrics
  totals:
    total_duration_ms: 16000
    total_tool_calls: 25
    total_tokens_used: 23500
    parallel_calls: 8  # tool calls that were parallelized
    cache_hits: 2  # symbols found in cache
    
  # Quality metrics
  quality:
    confidence_distribution:
      HIGH: 8
      MEDIUM: 3
      LOW: 1
    quality_check_passed: true
    quality_check_items: 9
    auto_fixes_applied: 0
  
  # Output summary
  output:
    module_doc: "docs/modules/03-auth.md"
    status_updated: true
    progress: 35
    completed_modules: ["startup", "routing", "auth"]
  
  # Errors (if any)
  errors: []
```

## Trace Collection Points

### Base Skill Traces

Each base skill emits trace at completion:

#### discover_project trace
```yaml
trace:
  skill: "discover_project"
  metrics:
    files_scanned: 87
    configs_read: 5
    entry_points_found: 2
    project_type: "frontend-react"
    analysis_mode: "moderate_analysis"
    tool_calls:
      list_directory: 1
      read_file: 5
      glob: 4
    tokens_used: 3500
```

#### analyze_symbol trace
```yaml
trace:
  skill: "analyze_symbol"
  metrics:
    symbol: "AuthService"
    file: "src/auth/service.ts"
    lines_analyzed: 185
    dependencies_traced: 5
    patterns_identified: 3
    confidence_distribution:
      HIGH: 2
      MEDIUM: 1
    tool_calls:
      read_file: 2
      agent Explore: 2
      grep_search: 1
    tokens_used: 12000
```

#### write_module_doc trace
```yaml
trace:
  skill: "write_module_doc"
  metrics:
    doc_path: "docs/modules/03-auth.md"
    sections_written: 8
    anchors_updated: 2
    quality_check_passed: true
    quality_items: 9
    tool_calls:
      read_file: 1
      edit: 1
    tokens_used: 8000
```

#### manage_status trace
```yaml
trace:
  skill: "manage_status"
  metrics:
    status_file: ".ai/status.yaml"
    fields_updated: 8
    cache_entries_added: 3
    validation_passed: true
    tool_calls:
      read_file: 1
      write_file: 1
    tokens_used: 1500
```

## Trace Storage

Traces are stored in `.ai/traces/` directory:

```
.ai/
├── status.yaml
├── traces/
│   ├── 20260414-001.yaml  # run_id
│   ├── 20260414-002.yaml
│   └── latest.yaml        # symlink to most recent
└── docs/
```

## Debug Mode

Users can request debug output:

### Enable Debug Mode
```
User requests:
- "启用调试模式"
- "启用 debug mode"
- "输出执行追踪"
- "show execution trace"
```

### Debug Output Format

When debug mode is enabled, skill outputs include:

#### 1. Execution Timeline
```markdown
## Execution Trace (Debug Mode)

**Run ID**: 20260414-001
**Start**: 2026-04-14T10:00:00Z
**Total Duration**: 16.0s

### Skill Execution Timeline

| Time | Skill | Duration | Status | Tokens |
|------|-------|----------|--------|--------|
| 0.0s | discover_project | 2.0s | ✅ | 3,500 |
| 2.0s | manage_status | 0.5s | ✅ | 1,500 |
| 2.5s | analyze_symbol | 8.0s | ✅ | 12,000 |
| 10.5s | write_module_doc | 6.0s | ✅ | 8,000 |
| 16.5s | manage_status | 0.5s | ✅ | 1,000 |

**Total**: 25 tool calls, 26,000 tokens, 8 parallelized
```

#### 2. Token Usage Breakdown
```markdown
## Token Usage

| Phase | Tokens | % of Total |
|-------|--------|------------|
| Project Discovery | 3,500 | 13.5% |
| Symbol Analysis | 12,000 | 46.2% |
| Doc Generation | 8,000 | 30.8% |
| Status Management | 2,500 | 9.6% |
| **Total** | **26,000** | **100%** |

Cache hits: 2 (saved ~3,000 tokens)
```

#### 3. Confidence Distribution
```markdown
## Confidence Distribution

| Level | Count | % |
|-------|-------|---|
| ✅ HIGH | 8 | 61.5% |
| ⚠️ MEDIUM | 3 | 23.1% |
| ❓ LOW | 2 | 15.4% |

**Average Confidence**: 0.77
```

#### 4. Tool Call Details
```markdown
## Tool Calls (Detailed)

### discover_project (2.0s)
1. `list_directory` → root structure (87 files)
2. `read_file` → package.json (parallel)
3. `read_file` → tsconfig.json (parallel)
4. `read_file` → vite.config.ts (parallel)
5. `glob` → **/main.ts (parallel)
6. `glob` → **/index.ts (parallel)
...

### analyze_symbol (8.0s)
1. `read_file` → src/auth/service.ts (lines 1-200)
2. `agent Explore` → trace AuthService dependencies
3. `grep_search` → find AuthService usages
...
```

## Performance Metrics

### Key Metrics to Track

#### Efficiency Metrics
- **tokens_per_module**: Average token consumption per module
  - Target: < 15,000 tokens/module
  - Alert if: > 25,000 tokens/module

- **parallelization_rate**: % of tool calls that were parallelized
  - Target: > 30%
  - Alert if: < 10%

- **cache_hit_rate**: % of symbols found in cache
  - Target: > 20% (for repeat sessions)
  - Alert if: 0% (cache not working)

- **duration_per_module**: Average time per module
  - Target: < 3 minutes
  - Alert if: > 8 minutes

#### Quality Metrics
- **confidence_distribution**: Ratio of HIGH/MEDIUM/LOW
  - Target: > 60% HIGH
  - Alert if: > 30% LOW

- **quality_check_pass_rate**: % of docs passing on first try
  - Target: > 80%
  - Alert if: < 50%

- **revision_count**: How many times docs are revised
  - Target: < 2 revisions/module
  - Alert if: > 5 revisions

## User Debug Requests

Handle these user requests with trace output:

### "显示 token 使用明细"
Output: Token Usage Breakdown table

### "为什么选择这个分析深度？"
Output:
```markdown
## Analysis Mode Decision

**Selected**: moderate_analysis
**Reason**: Project has 87 files (50-200 range)
**Config**: max_chunk_size=200, max_files_per_pass=50

**Alternatives considered**:
- deep_analysis: Would use ~2x tokens, not justified for this size
- shallow_first: Would lose important details, not needed

**User overrides**: None (used defaults)
```

### "显示缓存命中情况"
Output:
```markdown
## Cache Status

**Cache enabled**: true
**Entries in cache**: 12 symbols
**Hits this session**: 3
**Misses this session**: 5
**Hit rate**: 37.5%

**Cached symbols**:
- AuthService (HIGH confidence, analyzed 2026-04-14)
- Router (HIGH confidence, analyzed 2026-04-14)
- UserController (MEDIUM confidence, analyzed 2026-04-14)

**Cache misses** (analyzed this session):
- PaymentService (new symbol)
- OrderRepository (new symbol)
```

### "输出完整执行追踪"
Output: Full execution trace in YAML format

## Error Diagnostics

When errors occur, include diagnostic info:

### Skill Failure Format
```yaml
error:
  skill: "analyze_symbol"
  error_type: "file_not_found"
  message: "Cannot find src/auth/service.ts"
  context:
    attempted_paths:
      - "src/auth/service.ts"
      - "src/auth/Service.ts"
    glob_results:
      "**/auth/**": ["src/auth/index.ts", "src/auth/helpers.ts"]
    suggestions:
      - "AuthService might be in src/auth/index.ts"
      - "Try different symbol name or file path"
  recovery:
    attempted: "Fallback to grep_search for 'AuthService'"
    result: "Found in src/auth/index.ts, continuing analysis"
    status: "recovered"
```

## Trace Aggregation (Future)

For long-running projects, aggregate traces across sessions:

```yaml
trace_summary:
  total_runs: 15
  total_modules_analyzed: 8
  total_tokens_used: 185000
  average_tokens_per_module: 23125
  average_duration_per_module_s: 180
  most_expensive_module: "PaymentService (35,000 tokens)"
  cheapest_module: "Utils (8,000 tokens)"
  common_errors:
    - "file_not_found: 2 times"
    - "parse_error: 1 time"
  improvement_trend:
    - "Run 1-5: avg 28,000 tokens/module"
    - "Run 6-10: avg 22,000 tokens/module"
    - "Run 11-15: avg 19,000 tokens/module"
    - "Trend: -32% token usage (cache + optimization working)"
```

## Implementation Notes

- Traces are **optional** - only stored if user enables debug mode
- Traces are **append-only** - never modify old traces
- Traces are **self-contained** - include all context needed for debugging
- Traces respect **privacy** - do not include source code, only metadata
- Trace files are **small** - < 10KB each, cleanup after 100 traces
