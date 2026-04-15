---
name: quick_start
description: Composite skill that discovers project structure (with optional zread wiki integration) and generates initial technical skeleton. Fast path to get started.
version: "2.0"
category: "composite"
---

# Composite Skill: Quick Start

## Purpose
快速启动组合技能：混合发现（zread wiki + 传统分析）→ 生成技术骨架。适合首次使用 skill 的场景，速度提升 40-60%。

## Composition
```yaml
composition:
  type: "sequential"
  skills:
    - step_1:
        skill: "hybrid_discover"  # Changed from discover_project
        output: "project_map"
        options:
          use_zread: true  # Enable zread wiki generation
          confirm_generation: true  # Ask user before generation
    - step_2:
        skill: "manage_status"
        input: "project_map"
        action: "initialize_status"
    - step_3:
        skill: "write_module_doc"
        input: "project_map + status"
        target: "docs/project/tech-overview.md (skeleton only)"
```

## Execution Flow

### Step 1: Hybrid Discover Project
Run `hybrid_discover` skill:
- **Phase 1**: Zread wiki generation (if available)
  - Check if zread CLI installed
  - If `.zread/wiki/current` exists: read existing wiki
  - Otherwise: offer to generate wiki (requires user consent)
  - Parse wiki.json TOC and map to project modules
- **Phase 2**: Traditional discovery (always runs)
  - Scan workspace structure
  - Identify tech stack and entry points
  - Merge wiki insights with traditional analysis
  - Assess complexity and set analysis_mode
- **Output**: `project_map` with zread wiki integration

### Step 2: Initialize Status
Run `manage_status` skill:
- Create `.ai/status.yaml` with scaffold
- Populate metadata from project_map
- Set phase to `skeleton`
- **Initialize version control** (new in v2.0):
  - Create `.ai/versions/` directory
  - Record zread wiki version if available
  - Initialize draft state
- **Setup logging** (new in v2.0):
  - Create `.ai/log/` directory
  - Initialize discovery.log
- **Output**: initialized status with version management

### Step 3: Draft Technical Skeleton
Generate minimal tech-overview.md:
```markdown
# Project Technical Overview

## Project Goal
<!-- ANCHOR: project-goal -->
{Brief description from README or inference}

## Tech Stack and How to Run
<!-- ANCHOR: tech-stack-run -->
{Tech stack from package.json/configs}

## Entry and Lifecycle
<!-- ANCHOR: entry-lifecycle -->
{Entry points identified}

## Module Map
<!-- ANCHOR: module-map -->
{High-level module structure, enhanced with zread wiki data}

## Core Execution Flow
<!-- ANCHOR: core-execution-flow -->
{Mermaid flowchart of main flow}

## Key Patterns and Data Structures
<!-- ANCHOR: key-patterns-data-structures -->

## Open Questions
<!-- ANCHOR: open-questions -->
```

**Important**: Do NOT fill all details at once. Just create the skeleton with anchors.

## Post-conditions
```yaml
required:
  - status_yaml_created: true
  - tech_overview_skeleton_created: true
  - all_anchors_present: true
  - phase: "skeleton"
  - progress: 10
  - version_control_initialized: true  # new
  - log_files_created: true  # new
```

## When to Use
- First time running skill on a project
- User says: "帮我了解这个项目"
- User says: "先读 README 和入口文件，帮我做源码教学骨架"
- User says: "quick start"
- **User says: "用 zread 快速生成文档"** (new)

## Expected Duration
- **With zread wiki existing**: 20-40 seconds (read only)
- **With zread generation**: 2-10 minutes (depends on project size)
- Small project (<50 files): 30-60 seconds (traditional)
- Medium project (50-200 files): 1-2 minutes (traditional)
- Large project (>200 files): 2-3 minutes (traditional)

## Error Handling
```yaml
if_discover_fails:
  - "Fallback to config-only analysis"
  - "Create minimal status with project_type: unknown"
  - "Prompt user for manual input"

if_zread_fails:  # new
  - "Log error to .ai/log/error.log"
  - "Continue with traditional discovery"
  - "Record zread_failure_reason in status"

if_status_fails:
  - "Retry once with minimal scaffold"
  - "If still fails, report error and stop"

if_skeleton_fails:
  - "Create bare minimum: just the anchors"
  - "Record in status under doc_generation_issues"
  - "Log error for debugging"
```

## Zread Integration Benefits (new in v2.0)

### Speed Improvements
- **Avoids file-by-file crawling**: Zread generates wiki in parallel using LLM
- **Leverages generated content**: Read wiki.json instead of analyzing each file
- **40-60% faster** than traditional discovery for medium-large projects

### Version Management
- **Wiki versions**: Track zread wiki generations under `.zread/wiki/versions/`
- **Teaching doc versions**: Track our teaching docs under `.ai/versions/`
- **Draft recovery**: Resume from interrupted generations

### Logging & Debugging
- **Execution logs**: `.ai/log/` directory with structured logs
- **Error tracking**: Detailed error logs for troubleshooting
- **Token tracking**: Log token usage per skill for optimization
