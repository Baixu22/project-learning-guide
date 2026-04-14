# 📚 Documentation Index / 文档索引

> Complete guide to Project Learning Guide documentation

**🌐 Languages**: [English](INDEX.md) | [中文](INDEX.zh-CN.md)

---

## 🎯 Start Here / 从这里开始

| Document / 文档 | Purpose / 用途 | When to Read / 何时阅读 |
|-----------------|---------------|------------------------|
| **[README.md](README.md)** 🇬🇧 | Main introduction (English) | First time users |
| **[README.zh-CN.md](README.zh-CN.md)** 🇨🇳 | 主要介绍（中文） | 首次使用 |
| **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** | One-page command cheat sheet | Daily usage |
| **[ARCHITECTURE.md](ARCHITECTURE.md)** | Visual architecture diagrams | Understanding design |

---

## 📖 Core Documentation

### User-Facing Docs

1. **[README.md](README.md)** ⭐
   - **What**: Complete project introduction
   - **Contains**: Features, architecture, quick start, examples
   - **Audience**: All users
   - **Length**: ~15 min read

2. **[QUICK_REFERENCE.md](QUICK_REFERENCE.md)** 📋
   - **What**: One-page command reference
   - **Contains**: Commands, parameters, scenarios, tips
   - **Audience**: Regular users
   - **Length**: 3 min skim

3. **[ARCHITECTURE.md](ARCHITECTURE.md)** 🏗️
   - **What**: Visual system architecture
   - **Contains**: Mermaid diagrams, data flows, metrics
   - **Audience**: Developers, architects
   - **Length**: ~10 min read

---

## 🔧 Technical Documentation

### Skill Definitions

Located in `skills/` directory:

| File | Type | Description |
|------|------|-------------|
| `skills/discover_project.md` | Base Skill | Project discovery and mapping |
| `skills/analyze_symbol.md` | Base Skill | Deep symbol analysis |
| `skills/write_module_doc.md` | Base Skill | Document generation |
| `skills/manage_status.md` | Base Skill | State management |
| `skills/composite_quick_start.md` | Composite Skill | Fast start workflow |
| `skills/composite_deep_analysis.md` | Composite Skill | Deep dive workflow |
| `skills/composite_doc_maintenance.md` | Composite Skill | Doc maintenance workflow |

### Configuration & Specs

| File | Purpose |
|------|---------|
| `skills/config.yaml` | All configurable parameters with defaults |
| `skills/execution_trace.md` | Execution trace specification |

### References

Located in `references/` directory:

| File | Purpose |
|------|---------|
| `references/status-schema.md` | Status.yaml structure and rules |
| `references/doc-patterns.md` | Document template patterns |

---

## 📊 Optimization Docs

| File | Content |
|------|---------|
| [OPTIMIZATION_SUMMARY.md](OPTIMIZATION_SUMMARY.md) | Complete optimization summary (all 9 optimizations) |
| [PHASE1_OPTIMIZATION_SUMMARY.md](PHASE1_OPTIMIZATION_SUMMARY.md) | Phase 1 details (composability, config, tracing) |

---

## 🎓 Learning Path

### For New Users

```
1. README.md (Overview)
   ↓
2. QUICK_REFERENCE.md (Commands)
   ↓
3. Try: "帮我快速了解这个项目"
   ↓
4. ARCHITECTURE.md (How it works)
```

### For Developers

```
1. ARCHITECTURE.md (System design)
   ↓
2. SKILL.md (Skill definitions)
   ↓
3. skills/ directory (Skill implementations)
   ↓
4. references/ (Specs and patterns)
```

### For Contributors

```
1. PHASE1_OPTIMIZATION_SUMMARY.md (What was done)
   ↓
2. skills/ directory (Current structure)
   ↓
3. Add new skills (follow existing templates)
   ↓
4. Update README.md and config.yaml
```

---

## 📁 Complete File Structure

```
project-learning-guide/
│
├── 📄 README.md                          ⭐ Start here!
├── 📄 QUICK_REFERENCE.md                 📋 Quick commands
├── 📄 ARCHITECTURE.md                    🏗️ Visual diagrams
├── 📄 SKILL.md                           🔧 Main skill definition
├── 📄 INDEX.md                           📚 This file
│
├── 📊 OPTIMIZATION_SUMMARY.md            📈 All optimizations
├── 📊 PHASE1_OPTIMIZATION_SUMMARY.md     📈 Phase 1 details
│
├── 🧩 skills/                            # Skill modules
│   ├── discover_project.md               # Base skill
│   ├── analyze_symbol.md                 # Base skill
│   ├── write_module_doc.md               # Base skill
│   ├── manage_status.md                  # Base skill
│   ├── composite_quick_start.md          # Composite skill
│   ├── composite_deep_analysis.md        # Composite skill
│   ├── composite_doc_maintenance.md      # Composite skill
│   ├── config.yaml                       # Configuration
│   └── execution_trace.md                # Trace spec
│
├── 📚 references/                        # Reference docs
│   ├── status-schema.md                  # Status structure
│   └── doc-patterns.md                   # Doc templates
│
└── 🤖 agents/
    └── openai.yaml                       # Agent config
```

---

## 🔍 Find Information By Topic

| If you want to know... | Read this section |
|------------------------|-------------------|
| How to use the skill | README.md → Quick Start |
| Available commands | QUICK_REFERENCE.md → Common Commands |
| Configuration options | README.md → Configuration |
| Architecture design | ARCHITECTURE.md |
| Skill composition | README.md → Skill Architecture |
| Execution tracing | README.md → Execution Trace |
| Teaching strategies | README.md → Teaching Features |
| Performance metrics | README.md → Performance |
| Add new skills | PHASE1_OPTIMIZATION_SUMMARY.md → Maintenance Guide |
| Status format | references/status-schema.md |
| Document templates | references/doc-patterns.md |

---

## 📞 Quick Links

### Essential Reads
- [README.md - Main Guide](README.md)
- [QUICK_REFERENCE.md - Commands](QUICK_REFERENCE.md)
- [SKILL.md - Skill Definition](SKILL.md)

### Technical Docs
- [ARCHITECTURE.md - System Design](ARCHITECTURE.md)
- [skills/config.yaml - Configuration](skills/config.yaml)
- [skills/execution_trace.md - Tracing](skills/execution_trace.md)

### Development
- [skills/ - Skill Directory](skills/)
- [references/ - Reference Docs](references/)
- [PHASE1_OPTIMIZATION_SUMMARY.md - Phase 1](PHASE1_OPTIMIZATION_SUMMARY.md)

---

<div align="center">

**Documentation Index - Project Learning Guide**

*Last updated: 2026-04-14*

[README](README.md) • [Quick Reference](QUICK_REFERENCE.md) • [Architecture](ARCHITECTURE.md)

</div>
