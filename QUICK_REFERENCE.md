# 📋 Quick Reference Card

> One-page guide to using Project Learning Guide skill

---

## 🚀 Most Common Commands

| Command | Description | When to Use |
|---------|-------------|-------------|
| `帮我从这个已打开的项目工作区开始生成学习文档` | Quick start - generate initial docs | First time using skill |
| `帮我深入分析 XX 模块` | Deep dive into specific module | Understanding specific code |
| `根据当前 status 继续补全 teaching outline` | Continue from last progress | Resuming work |
| `只重写 <!-- ANCHOR: name --> 这一段` | Rewrite specific anchor | Targeted updates |
| `启用调试模式，显示 token 使用明细` | Enable debug mode | Troubleshooting |

---

## ⚙️ Essential Parameters Cheat Sheet

### Analysis Control
```
"使用 deep_analysis 模式"     → Deep analysis
"使用 shallow_first 模式"     → Quick scan
"max_chunk_size=100"          → Read smaller chunks
"max_files_per_pass=20"       → Limit files per run
```

### Language & Style
```
"language=zh-CN"              → Chinese docs
"language=en-US"              → English docs
"language=mixed"              → Mixed (default)
"detail_level=beginner"       → Simple explanations
"detail_level=advanced"       → Technical depth
```

### Content Control
```
"use_analogies=true"          → Include code analogies
"use_counter_examples=false"  → Skip counter-examples
"mermaid_threshold=3"         → Generate diagrams for 3+ modules
"max_snippet_length=20"       → Max code lines per snippet
```

### Performance
```
"max_context_tokens=6000"     → Limit token usage
"parallel_tool_calls=false"   → Disable parallel calls
"cache_enabled=false"         → Don't use cache
```

---

## 📁 Output Structure

```
project-root/
├── .ai/
│   ├── status.yaml              ← Progress & state
│   └── traces/                  ← Debug traces (optional)
│       └── 20260414-001.yaml
│
└── docs/
    ├── project/
    │   ├── tech-overview.md     ← Technical overview
    │   └── teaching-outline.md  ← Learning roadmap
    │
    └── modules/
        ├── 01-startup-bootstrap.md
        ├── 02-routing-flow.md
        └── 03-auth-module.md
```

---

## 🎯 Example Scenarios

### Scenario 1: New Team Member Onboarding
```
User: "帮我快速了解这个项目，language=zh-CN，detail_level=beginner"

Result:
- Chinese docs with simple explanations
- Project map with module relationships
- Good for someone new to the codebase
```

### Scenario 2: Deep Technical Study
```
User: "深入分析 auth 模块，detail_level=advanced，max_context_tokens=8000"

Result:
- In-depth analysis of authentication system
- Advanced technical details
- Design patterns and trade-offs
```

### Scenario 3: Documentation Maintenance
```
User: "根据当前 status 继续推进，use_analogies=true"

Result:
- Continues from last progress
- Generates next module doc
- Includes simplified code examples
```

### Scenario 4: Quick Exploration
```
User: "快速扫描，max_files_per_pass=15，不要生成 mermaid 图"

Result:
- Fast project overview
- Limited to 15 files
- No diagrams (faster)
```

---

## 🔍 Debug Commands

| Command | Output |
|---------|--------|
| `启用调试模式` | Full execution trace |
| `显示 token 使用明细` | Token breakdown by phase |
| `为什么选择这个分析深度？` | Reasoning for mode selection |
| `显示缓存命中情况` | Cache hit/miss statistics |
| `输出完整执行追踪` | Complete trace in YAML |

---

## 📊 Status Fields Quick Reference

```yaml
# .ai/status.yaml essential fields

project_type: "frontend-react"       # Detected project type
phase: "module-writing"              # Current phase
progress: 35                         # 0-100 completion %
current_focus: "routing-flow"        # What we're working on
analysis_mode: "moderate_analysis"   # Analysis depth mode

key_files:                           # Important files analyzed
  - "src/main.ts"
  - "src/router/index.ts"

completed_modules:                   # Finished modules
  - "startup bootstrap"
  - "routing flow"

llm_context:                         # Context cache
  analyzed_symbols:
    - name: "AuthService"
      file: "src/auth/service.ts"
      summary: "处理认证逻辑"
      confidence: "HIGH"
```

---

## 🎓 Quality Checklist

Each module doc should have:
- [ ] File paths + symbol names
- [ ] No code dumps (>20 lines)
- [ ] Why design, not just What
- [ ] Confidence annotations (HIGH/MED/LOW)
- [ ] 2-3 practice tasks
- [ ] Few-shot analogy (for complex patterns)
- [ ] Counter-example table
- [ ] Mermaid diagram (if >3 modules)
- [ ] Mermaid nodes in double quotes

---

## 🚦 Common Workflows

### First Time Usage
```
1. "帮我快速了解这个项目"
   → Generates skeleton docs

2. "帮我深入分析 auth 模块"
   → Deep dive into auth

3. "继续推进到下一个模块"
   → Auto-continues
```

### Maintenance Mode
```
1. "根据当前 status 继续补全 teaching outline"
   → Resumes from progress

2. "只重写 entry-lifecycle 这个锚点"
   → Targeted rewrite
```

### Debugging
```
1. "启用调试模式"
   → Shows execution details

2. "显示为什么选择这个分析深度"
   → Explains reasoning
```

---

## 💡 Pro Tips

1. **Start shallow, go deep later**
   - First: `shallow_first` mode
   - Then: `deep_analysis` on interesting modules

2. **Use cache across sessions**
   - Status is preserved in `.ai/status.yaml`
   - Says "continue from last time" to resume

3. **Customize for audience**
   - Beginners: `detail_level=beginner`, `language=zh-CN`
   - Experts: `detail_level=advanced`, `use_counter_examples=true`

4. **Monitor token usage**
   - Debug mode shows token breakdown
   - Adjust `max_context_tokens` if needed

5. **Quality over quantity**
   - Skill auto-checks quality
   - Don't disable quality checks unless necessary

---

<div align="center">

**Quick Reference Card - Project Learning Guide**

*Print or bookmark for easy access*

</div>
