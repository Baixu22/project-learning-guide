---
name: manage_status
description: Create, update, and maintain .ai/status.yaml. Handles context cache and execution state.
version: "1.0"
category: "base"
---

# Skill: Manage Status

## Purpose
创建、更新和维护 `.ai/status.yaml`。处理上下文缓存和执行状态追踪。

## Pre-conditions
```yaml
required:
  - workspace_root: "valid path"
optional:
  - existing_status: ".ai/status.yaml"  # 如果存在，读取并更新；否则创建
```

## Execution Steps

### 1. Load Existing Status (if any)
1. Check if `.ai/status.yaml` exists
2. If exists:
   - Read and parse YAML
   - Validate structure (required fields present)
   - If corrupted: rename to `.ai/status.yaml.bak.<timestamp>` and create new
3. If not exists:
   - Create minimal scaffold

### 2. Update Status
Based on input, update relevant fields:

#### Phase Transitions
```yaml
phase_flow:
  scoping → skeleton → outlining → module-writing → refining
```

Only move forward, never backward unless user explicitly requests reset.

#### Progress Tracking
- Increment by 10-20 per completed module
- Never set to 100 until all planned modules done
- Update `completed_modules` list

#### Context Cache Management
```yaml
llm_context:
  analyzed_symbols:
    # Add new symbols after module analysis
    - name: "AuthService"
      file: "src/auth/service.ts"
      summary: "处理认证逻辑"
      confidence: "HIGH"
      analyzed_at: "2026-04-14T10:00:00Z"
  
  reasoning_cache:
    # Store derived conclusions to avoid re-deriving
    Q1_auth_flow: "已分析完毕，见 docs/modules/03-auth.md"
  
  attention_hotspots:
    # Track current focus areas
    - "src/auth/**"
```

#### Update Triggers
Update status when:
- Phase changes
- New anchor rewritten
- Module doc completed
- Key files become clearer
- Runtime-only edges discovered
- Context cache updated

### 3. Validate Status
After update, check:
- [ ] All required fields present?
- [ ] No long prose or code blocks?
- [ ] `completed_modules` has one line per module?
- [ ] `progress` matches completed module count?
- [ ] `anchors_touched` updated?
- [ ] `llm_context` summaries < 20 words each?

### 4. Write Status
- Write valid YAML to `.ai/status.yaml`
- If update failed:
  - Keep backup of old version
  - Write minimal valid status
  - Report error to user

## Output
```yaml
# .ai/status.yaml
project_type: "frontend-react"
phase: "module-writing"
progress: 35
current_focus: "routing-flow"
analysis_mode: "moderate_analysis"
key_files:
  - "src/main.ts"
  - "src/router/index.ts"
completed_modules:
  - "startup bootstrap"
question_ids:
  - "Q1"
anchors_touched:
  - "entry-lifecycle"
  - "module-map"
doc_paths:
  tech_overview: "docs/project/tech-overview.md"
  teaching_outline: "docs/project/teaching-outline.md"
  modules_dir: "docs/modules"
runtime_resolved:
  - "src/modules/registry.ts -> dynamic import()"
readme_insufficient: false
llm_context:
  analyzed_symbols:
    - name: "AuthService"
      file: "src/auth/service.ts"
      summary: "处理认证逻辑"
      confidence: "HIGH"
  reasoning_cache: {}
  attention_hotspots:
    - "src/auth/**"
```

## Post-conditions
```yaml
required:
  - status_yaml_exists: true
  - valid_yaml: true
  - required_fields_present: true
  - no_long_prose: true
```

## On-failure
```yaml
retry:
  max_attempts: 1  # Only retry once to avoid loops
degrade:
  - "If YAML corrupted → backup and create minimal scaffold"
  - "If write failed → report error, do not retry indefinitely"
fallback:
  - "Return error message with recovery suggestions"
  - "Prompt user to manually verify status file"
```

## Execution Trace
```yaml
trace:
  skill: "manage_status"
  tool_calls:
    - read_file: "0-1 (if exists)"
    - write_file: "1"
  expected_duration_ms: 500-1500
  expected_tokens: 1000-2000
  validation_items: 6
```
