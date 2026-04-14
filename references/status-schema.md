# Status Schema Reference

Use this file only when you need the exact constraints for `.ai/status.yaml`.

## Goals

- Preserve progress and context in a compact form
- Keep signal-to-noise ratio extremely high
- Avoid duplicating technical analysis

## Hard rules

- YAML only
- Store metadata only
- No long explanations
- No code blocks
- No more than one concise line per completed module
- Question details belong in markdown docs, not in status

## Suggested schema

```yaml
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
  - "Q2"
anchors_touched:
  - "routing-flow"
doc_paths:
  tech_overview: "docs/project/tech-overview.md"
  teaching_outline: "docs/project/teaching-outline.md"
  modules_dir: "docs/modules"
runtime_resolved:
  - "src/pages/index.ts -> dynamic import()"
readme_insufficient: false

# Optional: context cache for LLM efficiency (large projects or multi-session)
llm_context:
  analyzed_symbols:
    - name: "AuthService"
      file: "src/auth/service.ts"
      summary: "处理认证逻辑"
      confidence: "HIGH"
  reasoning_cache:
    Q1_auth_flow: "已分析完毕，见 docs/modules/03-auth.md"
  attention_hotspots:
    - "src/auth/**"
```

## New fields explanation

### analysis_mode
- `deep_analysis`: Small projects (<50 files), or user requests deep dive
- `moderate_analysis`: Medium projects (50-200 files), module by module
- `shallow_first`: Large projects (>200 files), skeleton first then ask

### llm_context (optional)
Use for projects >100 files or when user says "continue from last time".
Stores already-analyzed symbols to avoid re-analysis and save tokens.

## Update checklist

Update status when:
- phase changes
- a new anchor is rewritten
- a module doc is completed
- key files become clearer
- runtime-only edges are discovered

Do not update status just to restate reasoning already written elsewhere.
