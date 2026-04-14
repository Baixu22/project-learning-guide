---
name: doc_maintenance
description: Composite skill for maintaining and updating existing documentation. Reads status, updates docs, and manages anchors.
version: "1.0"
category: "composite"
---

# Composite Skill: Doc Maintenance

## Purpose
文档维护组合技能：读取状态 → 更新指定文档 → 管理锚点。适合继续编写、修订文档的场景。

## Composition
```yaml
composition:
  type: "conditional"
  decision_tree:
    - if: "user requests specific anchor update"
      then:
        type: "sequential"
        skills:
          - skill: "read_status"
          - skill: "rewrite_anchor"
          - skill: "update_status"
    
    - if: "user requests continue to next module"
      then:
        type: "sequential"
        skills:
          - skill: "read_status"
          - skill: "identify_next_module"
          - skill: "deep_analysis"
    
    - if: "user requests refactor existing doc"
      then:
        type: "sequential"
        skills:
          - skill: "read_existing_doc"
          - skill: "add_anchors_if_missing"
          - skill: "rewrite_targeted_sections"
          - skill: "quality_check"
```

## Execution Flow

### Scenario 1: Rewrite Specific Anchor

#### Step 1: Read Status
- Load `.ai/status.yaml`
- Identify `current_focus` and `anchors_touched`
- Check if target anchor exists in doc

#### Step 2: Rewrite Anchor
- Read relevant source code
- Rewrite ONLY content between `<!-- ANCHOR: {name} -->` and next anchor
- Keep surrounding text and headings unchanged
- Apply quality rules (confidence, analogy, etc.)

#### Step 3: Update Status
- Add anchor to `anchors_touched`
- Update `current_focus` if needed
- Increment progress slightly (2-5 points)

### Scenario 2: Continue to Next Module

#### Step 1: Read Status
- Load `.ai/status.yaml`
- Check `completed_modules` and `progress`
- Read `teaching-outline.md` to identify next module

#### Step 2: Identify Next Module
- Find first incomplete module from outline
- Extract module name, source target, and focus pattern
- If all modules done → suggest refining phase

#### Step 3: Run Deep Analysis
- Delegate to `deep_analysis` composite skill
- Follow module analysis workflow
- Update status upon completion

### Scenario 3: Refactor Existing Doc

#### Step 1: Read Existing Doc
- Load target document
- Check for anchors
- If no anchors → suggest adding them first

#### Step 2: Add Anchors (if missing)
- Identify logical sections
- Insert anchor comments:
  ```markdown
  <!-- ANCHOR: section-name -->
  ```
- Update status with new anchors

#### Step 3: Rewrite Targeted Sections
- User specifies which sections to rewrite
- For each section:
  - Re-read relevant source code
  - Rewrite with improved content
  - Apply quality checklist

#### Step 4: Quality Check
- Run full quality checklist on modified sections
- Ensure all quality items pass
- If fails → auto-fix

## Post-conditions
```yaml
required:
  - doc_updated: true
  - anchors_valid: true
  - status_updated: true
  - quality_check_passed: true
```

## When to Use
- User says: "根据当前 status 继续补全 teaching outline"
- User says: "只重写 `<!-- ANCHOR: routing-flow -->` 这一段"
- User says: "继续推进到下一个模块"
- User says: "帮我完善已生成的文档"

## Expected Duration
- Single anchor rewrite: 30-60 seconds
- Continue to next module: same as deep_analysis
- Refactor doc: 1-3 minutes depending on scope

## Error Handling
```yaml
if_anchor_not_found:
  - "Suggest available anchors"
  - "Offer to add anchors first"
  - "Fallback to section-level rewrite"

if_doc_corrupted:
  - "Restore from last known good state"
  - "If no backup, prompt user"
  - "Do not attempt auto-repair on corrupted files"

if_quality_check_fails:
  - "Auto-fix using rules from write_module_doc skill"
  - "If still fails, report specific issues"
  - "Do not publish low-quality content"
```

## Anchor Management Rules

### Anchor Naming
- Use kebab-case: `entry-lifecycle`, `module-map`
- Be descriptive but concise
- Prefix with domain if ambiguous: `auth-middleware`, `db-connection`

### Anchor Placement
- Always after section heading
- Before section content
- One anchor per logical section

### Anchor Validation
After any rewrite:
- [ ] Anchor comment syntax correct?
- [ ] No duplicate anchors in same file?
- [ ] Anchor name matches status.yaml `anchors_touched`?
- [ ] Content between anchors is coherent?
