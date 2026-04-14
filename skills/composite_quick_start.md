---
name: quick_start
description: Composite skill that discovers project structure and generates initial technical skeleton. Fast path to get started.
version: "1.0"
category: "composite"
---

# Composite Skill: Quick Start

## Purpose
快速启动组合技能：发现项目结构 → 生成技术骨架。适合首次使用 skill 的场景。

## Composition
```yaml
composition:
  type: "sequential"
  skills:
    - step_1:
        skill: "discover_project"
        output: "project_map"
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

### Step 1: Discover Project
Run `discover_project` skill:
- Scan workspace structure
- Identify tech stack and entry points
- Assess complexity and set analysis_mode
- **Output**: `project_map`

### Step 2: Initialize Status
Run `manage_status` skill:
- Create `.ai/status.yaml` with scaffold
- Populate metadata from project_map
- Set phase to `skeleton`
- **Output**: initialized status

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
{High-level module structure}

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
```

## When to Use
- First time running skill on a project
- User says: "帮我了解这个项目"
- User says: "先读 README 和入口文件，帮我做源码教学骨架"
- User says: "quick start"

## Expected Duration
- Small project (<50 files): 30-60 seconds
- Medium project (50-200 files): 1-2 minutes
- Large project (>200 files): 2-3 minutes

## Error Handling
```yaml
if_discover_fails:
  - "Fallback to config-only analysis"
  - "Create minimal status with project_type: unknown"
  - "Prompt user for manual input"

if_status_fails:
  - "Retry once with minimal scaffold"
  - "If still fails, report error and stop"

if_skeleton_fails:
  - "Create bare minimum: just the anchors"
  - "Record in status under doc_generation_issues"
```
