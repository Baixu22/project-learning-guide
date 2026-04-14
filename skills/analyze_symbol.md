---
name: analyze_symbol
description: Deep-dive analysis of a specific symbol (function, class, module). Traces dependencies, identifies patterns, and extracts design rationale.
version: "1.0"
category: "base"
---

# Skill: Analyze Symbol

## Purpose
深入分析单个符号（函数、类、模块），追踪依赖链，识别设计模式，提取设计意图。

## Pre-conditions
```yaml
required:
  - symbol_path: "valid file path and symbol name"
  - project_map_available: true  # requires discover_project
optional:
  - context_cache: "llm_context.analyzed_symbols"  # 检查是否已分析过
```

## Execution Steps

### 1. Load Symbol Context
1. `read_file` the containing file (chunk-based if > 300 lines)
2. Use `agent` with `Explore` to:
   - Find symbol definition
   - Trace import dependencies
   - Find usage locations

### 2. Analyze Structure
For each symbol, extract:
- **Signature**: parameters, return type, generics
- **Dependencies**: imports, injected services, global state
- **Internal Logic**: control flow, data transformations
- **Side Effects**: I/O, mutations, event emissions

### 3. Identify Patterns
Match against known patterns:
- **Design Patterns**: Factory, Singleton, Observer, Strategy, DI, etc.
- **Architectural Patterns**: Middleware, Plugin, Layer, Service-Repository, etc.
- **Data Patterns**: State Machine, Event Sourcing, CQRS, etc.

For each match, record confidence:
- ✅ HIGH_CONFIDENCE: Explicit implementation
- ⚠️ MEDIUM_CONFIDENCE: Partial match, reasonable assumption
- ❓ LOW_CONFIDENCE: Inferred from structure, runtime-dependent

### 4. Extract Design Rationale
Answer:
- **Why this design?**: What problem does it solve?
- **What trade-offs?**: What was sacrificed? (complexity vs performance, etc.)
- **What alternatives?**: What other approaches exist and why not chosen?

### 5. Generate Teaching Content
- Create **few-shot analogy**: 10-15 line simplified version
- Create **counter-example**: What if designed differently?
- Create **mermaid flowchart**: If internal logic has > 3 branches

## Output
```yaml
symbol_analysis:
  name: "AuthService"
  file: "src/auth/service.ts"
  lines: "45-230"
  signature: "class AuthService { login(), logout(), refresh() }"
  
  dependencies:
    imports:
      - "HttpClient from src/http/client.ts"
      - "Storage from src/utils/storage.ts"
    injects:
      - "config: AuthConfig"
    uses_global:
      - "window.localStorage"
  
  patterns_identified:
    - pattern: "Service Layer Pattern"
      confidence: "HIGH"
      evidence: "Encapsulates business logic, depends on repository"
    
    - pattern: "State Management"
      confidence: "MEDIUM"
      evidence: "Maintains session state, but not explicit state machine"
  
  design_rationale:
    why: "Separates auth logic from components for testability"
    trade_offs: "Added abstraction layer, but enables easy mocking"
    alternatives: "Could inline in components, but would be harder to test"
  
  teaching_content:
    analogy: "简化版 12 行代码示例"
    counter_example: "如果不这样设计的对比"
    mermaid: "flowchart showing auth flow"
  
  complexity_score: 7  # 1-10 scale
```

## Post-conditions
```yaml
required:
  - symbol_analyzed: true
  - dependencies_traced: true
  - patterns_identified: ">= 1"
  - confidence_annotated: true
```

## On-failure
```yaml
retry:
  max_attempts: 2
degrade:
  - "If symbol too complex (>500 lines) → analyze signature only"
  - "If dependencies unclear → mark as LOW_CONFIDENCE"
fallback:
  - "Return minimal analysis: signature + file location"
  - "Record in unresolved_questions for later manual review"
```

## Execution Trace
```yaml
trace:
  skill: "analyze_symbol"
  tool_calls:
    - read_file: "1-3 (chunk-based)"
    - agent Explore: "1-2"
    - grep_search: "0-2 (targeted)"
  expected_duration_ms: 5000-10000
  expected_tokens: 8000-15000
  complexity_threshold: "If >10 dependencies, mark as high_complexity"
```
