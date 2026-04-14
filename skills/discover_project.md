---
name: discover_project
description: Discover and map project structure from workspace root. Identifies tech stack, entry points, and module relationships.
version: "1.0"
category: "base"
---

# Skill: Discover Project

## Purpose
快速扫描项目结构，识别技术栈、入口文件和模块关系。作为所有其他技能的前置依赖。

## Pre-conditions
```yaml
required:
  - workspace_open: true
  - has_readable_structure: true
optional:
  - status_yaml_exists: ".ai/status.yaml"  # 如果存在，先读取恢复上下文
```

## Execution Steps

### 1. Initial Scan
1. `list_directory` on workspace root
2. Read `README.md` (if exists)
3. **Parallel**: Read ecosystem config files
   - Node: `package.json`, `tsconfig.json`, `vite.config.*`, `next.config.*`
   - Python: `pyproject.toml`, `requirements.txt`, `manage.py`, `app.py`
   - Java: `pom.xml`, `build.gradle*`
   - Go: `go.mod`
   - Rust: `Cargo.toml`

### 2. Identify Entry Points
1. **Parallel**: `glob` for entry patterns
   - `**/main.{ts,js,py,go,rs}`
   - `**/index.{ts,js}`
   - `**/app.{ts,js,py}`
2. **Parallel**: `glob` for route files
   - `**/router/**`
   - `**/routes/**`
   - `**/controllers/**`

### 3. Infer Project Type
Based on configs and entries, classify:
- `frontend-react`, `frontend-vue`, `frontend-next`
- `backend-node`, `backend-python`, `backend-java`, `backend-go`
- `ai-llm`, `rag-system`, `training-pipeline`
- `cli-tool`, `library`, `fullstack`

### 4. Assess Complexity
Count files and directories:
- < 50 files → `deep_analysis` mode
- 50-200 files → `moderate_analysis` mode
- > 200 files → `shallow_first` mode

## Output
```yaml
project_map:
  project_type: "frontend-react"
  analysis_mode: "moderate_analysis"
  tech_stack:
    - "React 18"
    - "TypeScript 5"
    - "Vite 5"
  entry_points:
    - "src/main.ts"
    - "src/router/index.ts"
  key_files:
    - "package.json"
    - "vite.config.ts"
    - "src/main.ts"
  module_candidates:
    - "src/modules/**"
    - "src/components/**"
  runtime_resolved:
    - "src/modules/registry.ts -> dynamic import()"
  file_count: 87
  complexity: "medium"
```

## Post-conditions
```yaml
required:
  - project_type_identified: true
  - entry_points_found: ">= 1"
  - analysis_mode_set: true
```

## On-failure
```yaml
retry:
  max_attempts: 2
  backoff: "exponential"
degrade:
  - "If README missing → mark readme_insufficient: true"
  - "If no entry points → fallback to config-only analysis"
  - "If ambiguous project type → record best_guess + uncertainty"
fallback:
  - "Return minimal structure: root directory + configs"
  - "Prompt user for manual project type specification"
```

## Execution Trace
```yaml
trace:
  skill: "discover_project"
  tool_calls:
    - list_directory: 1
    - read_file: "3-5 (parallel)"
    - glob: "2-4 (parallel)"
  expected_duration_ms: 1500-3000
  expected_tokens: 2000-4000
```
