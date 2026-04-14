---
name: deep_analysis
description: Composite skill for deep-dive analysis of a specific module. Discovers, analyzes, and documents one module at a time.
version: "1.0"
category: "composite"
---

# Composite Skill: Deep Analysis

## Purpose
深度分析组合技能：发现模块 → 深入分析 → 生成教学文档。适合逐个模块深入学习的场景。

## Composition
```yaml
composition:
  type: "sequential"
  skills:
    - step_1:
        skill: "discover_project"
        condition: "if not already done"
        output: "project_map"
    - step_2:
        skill: "analyze_symbol"
        input: "project_map + target_module"
        output: "symbol_analysis"
    - step_3:
        skill: "write_module_doc"
        input: "symbol_analysis + teaching_outline"
        output: "module_doc"
    - step_4:
        skill: "manage_status"
        input: "module_doc output"
        action: "update_progress + update_context_cache"
```

## Execution Flow

### Step 1: Ensure Project Map Exists
Check status.yaml:
- If `phase` is `scoping` or missing → run `discover_project`
- If `phase` is already `module-writing` → skip, use existing project_map

### Step 2: Analyze Target Module
Run `analyze_symbol` skill:
- Read module source code (chunk-based)
- Trace dependencies and usages
- Identify patterns and design rationale
- Generate few-shot analogy and counter-examples
- **Output**: detailed symbol_analysis

### Step 3: Write Module Document
Run `write_module_doc` skill:
- Use symbol_analysis to fill sections
- Apply teaching strategies (analogy, counter-example)
- Add confidence annotations
- Run quality checklist
- **Output**: complete module doc

### Step 4: Update Status
Run `manage_status` skill:
- Add module to `completed_modules`
- Increment progress by 10-20
- Add analyzed symbols to `llm_context.analyzed_symbols`
- Update `anchors_touched`
- Set phase to `module-writing` (if not already)
- **Output**: updated status

## Post-conditions
```yaml
required:
  - module_doc_created: "docs/modules/XX-module-name.md"
  - status_updated: true
  - progress_incremented: true
  - context_cache_updated: true
  - phase: "module-writing"
  - completed_modules_contains: "{module_name}"
```

## When to Use
- User says: "帮我深入分析 XX 模块"
- User says: "继续推进到下一个模块"
- After quick_start, user chooses a module to dive deep
- User wants detailed understanding of specific module

## Expected Duration
- Simple module: 1-2 minutes
- Complex module: 3-5 minutes
- Ultra-complex module (>500 lines): 5-8 minutes

## Error Handling
```yaml
if_analysis_fails:
  - "Degrade to signature-only analysis"
  - "Mark as LOW_CONFIDENCE"
  - "Continue to doc generation with minimal content"

if_doc_fails:
  - "Generate skeleton doc with TODO placeholders"
  - "Record issue in status"
  - "Prompt user for manual review"

if_status_fails:
  - "Retry once"
  - "If still fails, report error"
  - "Do not block doc generation"
```

## Context Cache Strategy
After successful analysis:
```yaml
llm_context:
  analyzed_symbols:
    - name: "{module_symbol}"
      file: "{module_file}"
      summary: "{1-2 sentence summary}"
      confidence: "{HIGH|MEDIUM|LOW}"
      analyzed_at: "{timestamp}"
  
  reasoning_cache:
    "{module_id}": "已分析完毕，见 docs/modules/XX-module-name.md"
```

This avoids re-analyzing the same module in future sessions.
