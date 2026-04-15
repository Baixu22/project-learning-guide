---
name: hybrid_discover
description: Hybrid discovery skill that integrates zread CLI for fast wiki generation, then enhances with teaching-oriented analysis. Combines speed of automated wiki with depth of manual teaching content.
version: "2.0"
category: "base"
---

# Skill: Hybrid Discover

## Purpose
混合发现技能：集成 zread CLI 快速生成 wiki 作为前置步骤，然后在此基础上增强教学内容。适合快速了解项目 + 深度教学的混合需求。

## Pre-conditions
```yaml
required:
  - workspace_open: true
  - zread_installed: "zread CLI available in PATH"  # 可选，未安装时降级到传统模式
optional:
  - existing_zread_wiki: ".zread/wiki/current"  # 如存在，直接读取
  - status_yaml_exists: ".ai/status.yaml"
```

## Execution Steps

### Phase 1: Zread Wiki Generation (if available)

#### Step 1.1: Check Zread Installation
1. Run `zread version` to verify installation
2. If not installed:
   - Log warning to `.ai/log/discovery.log`
   - Skip to Phase 2 (traditional discovery)
   - Record `zread_available: false` in status

#### Step 1.2: Check Existing Wiki
1. Check if `.zread/wiki/current` exists
2. If exists:
   - Read `.zread/wiki/current` for version ID
   - Read `.zread/wiki/versions/<id>/wiki.json` for TOC
   - **Skip generation**, use existing wiki
   - Record `wiki_source: "existing"` in status

#### Step 1.3: Generate Wiki (if no existing wiki)
1. **Confirm with user** (generation is long-running and costs LLM tokens)
2. Check login status:
   - Verify `~/.zread/login.json` or `~/.zread/config.yaml` has `llm.api_key`
   - If missing: run `zread login` (opens browser for OAuth)
3. Check for drafts:
   - If `.zread/wiki/drafts/` exists:
     - Offer: `zread generate --draft resume -y` (recover)
     - Or: `zread generate --draft clear -y` (start fresh)
   - Otherwise: `zread generate -y`
4. Optional: Add `--skip-failed` to not block on failing pages
5. Wait for generation to complete
6. Read generated wiki from `.zread/wiki/versions/<id>/wiki.json`

#### Step 1.4: Parse Wiki Content
1. Load `wiki.json` TOC
2. Extract page structure:
   - `{slug, title, file, section, group, level}`
3. Map to project modules
4. Record wiki version in status

### Phase 2: Traditional Discovery (always runs)

#### Step 2.1: Initial Scan
1. `list_directory` on workspace root
2. Read `README.md` (if exists)
3. **Parallel**: Read ecosystem config files
   - Node: `package.json`, `tsconfig.json`, `vite.config.*`
   - Python: `pyproject.toml`, `requirements.txt`
   - etc.

#### Step 2.2: Merge Wiki + Traditional Data
1. Compare zread wiki modules with traditional analysis
2. Identify gaps:
   - Zread wiki may lack: teaching analogies, counter-examples, practice tasks
   - Traditional analysis may lack: comprehensive module coverage
3. Create merged project map

#### Step 2.3: Identify Entry Points & Assess Complexity
Same as `discover_project`:
1. `glob` for entry patterns
2. Classify project type
3. Assess complexity and set analysis_mode

## Output
```yaml
project_map:
  project_type: "frontend-react"
  analysis_mode: "moderate_analysis"
  
  # Zread wiki integration
  zread_wiki:
    available: true
    version: "2026-04-14-001234"
    wiki_path: ".zread/wiki/versions/2026-04-14-001234"
    pages_count: 15
    generation_completed_at: "2026-04-14T10:30:00Z"
    skipped_pages: 2  # from --skip-failed
  
  # Traditional analysis
  tech_stack:
    - "React 18"
    - "TypeScript 5"
  entry_points:
    - "src/main.ts"
  key_files:
    - "package.json"
    - "src/main.ts"
  
  # Merged insights
  module_candidates:
    - "src/modules/**"  # from both sources
  coverage:
    zread_modules: 15
    teaching_modules_needed: 3  # gaps to fill
```

## Post-conditions
```yaml
required:
  - project_type_identified: true
  - entry_points_found: ">= 1"
  - analysis_mode_set: true
  - zread_wiki_processed: "true if available"
```

## On-failure
```yaml
retry:
  max_attempts: 2
degrade:
  - "If zread not installed → fallback to traditional discovery only"
  - "If zread generation fails → log error, continue with traditional"
  - "If wiki.json corrupted → report error, skip wiki integration"
fallback:
  - "Return traditional project_map without wiki data"
  - "Record zread_failure_reason in status"
```

## Zread Integration Guide

### Installation (for users)
```bash
# npm
npm install -g zread_cli

# Homebrew
brew tap ZreadAI/homebrew-tap
brew install zread
```

### Common Commands
```bash
# Generate wiki (with user consent)
zread generate -y

# Resume interrupted generation
zread generate --draft resume -y

# Skip failing pages
zread generate -y --skip-failed

# Browse locally
zread browse

# Programmatic mode
zread generate -y --stdio
```

## Execution Trace
```yaml
trace:
  skill: "hybrid_discover"
  phases:
    - phase_1_zread:
        tool_calls:
          - run_shell_command: "1-2 (zread version, generate)"
          - read_file: "1-2 (wiki.json, current)"
        expected_duration_ms: 10000-60000  # varies by project size
        expected_tokens: 3000-5000  # only for reading wiki, not generating
    
    - phase_2_traditional:
        tool_calls:
          - list_directory: 1
          - read_file: "3-5 (parallel)"
          - glob: "2-4 (parallel)"
        expected_duration_ms: 1500-3000
        expected_tokens: 2000-4000
  
  benefits:
    - "Avoids file-by-file crawling"
    - "Leverages LLM-generated wiki"
    - "Faster than traditional discovery by 40-60%"
```

## Version Management

### On-disk Layout
```
project-root/
├── .zread/
│   └── wiki/
│       ├── current                    # Active version ID
│       ├── versions/
│       │   └── <id>/
│       │       ├── wiki.json          # TOC
│       │       └── *.md               # Pages
│       └── drafts/                    # In-progress generation
│
├── .ai/
│   ├── status.yaml                    # Learning progress
│   ├── versions/                      # Teaching doc versions (our addition)
│   │   └── <id>/
│   │       ├── tech-overview.md
│   │       └── teaching-outline.md
│   └── log/
│       └── discovery.log              # Execution log (our addition)
```

### Version Lifecycle
1. **Draft**: Generation in progress (`.zread/wiki/drafts/`)
2. **Completed**: Generation finished (`.zread/wiki/versions/<id>/`)
3. **Active**: Currently used version (`.zread/wiki/current`)
4. **Archived**: Old versions (can be cleaned up manually)
