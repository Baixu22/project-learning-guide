# 🚀 Architecture Overview

> Visual guide to Project Learning Guide skill architecture

---

## 🎨 System Architecture Diagram

```mermaid
graph TB
  subgraph Users["👥 Users"]
    U1["🎓 Beginners"]
    U2["👨‍💻 Interns"]
    U3["🔄 Domain Switchers"]
  end

  subgraph Interface["💬 Interface Layer"]
    I1["Natural Language Requests"]
    I2["Configuration Parameters"]
    I3["Debug Commands"]
  end

  subgraph Skills["🧩 Skill Layer (Superpowers Architecture)"]
    subgraph Base["Base Skills (Atomic)"]
      B1["🔍 discover_project"]
      B2["🔬 analyze_symbol"]
      B3["📝 write_module_doc"]
      B4["💾 manage_status"]
    end
    
    subgraph Composite["Composite Skills (Workflows)"]
      C1["⚡ quick_start"]
      C2["🔬 deep_analysis"]
      C3["🔧 doc_maintenance"]
    end
  end

  subgraph Config["⚙️ Configuration Layer"]
    CF1["config.yaml<br/>90% behaviors configurable"]
    ET1["execution_trace.md<br/>Full observability"]
  end

  subgraph Storage["💿 Storage Layer"]
    S1[".ai/status.yaml<br/>State management"]
    S2[".ai/traces/<br/>Execution traces"]
    S3["docs/<br/>Generated documents"]
  end

  U1 & U2 & U3 --> I1 & I2 & I3
  I1 & I2 & I3 --> C1 & C2 & C3
  C1 --> B1 & B4 & B3
  C2 --> B1 & B2 & B3 & B4
  C3 --> B4 & B3
  CF1 -.-> B1 & B2 & B3 & B4
  ET1 -.-> B1 & B2 & B3 & B4
  B1 & B2 & B3 & B4 --> S1 & S2 & S3

  style U1 fill:#e1f5ff
  style U2 fill:#e1f5ff
  style U3 fill:#e1f5ff
  style I1 fill:#fff3e0
  style I2 fill:#fff3e0
  style I3 fill:#fff3e0
  style B1 fill:#e8f5e9
  style B2 fill:#e8f5e9
  style B3 fill:#e8f5e9
  style B4 fill:#e8f5e9
  style C1 fill:#f3e5f5
  style C2 fill:#f3e5f5
  style C3 fill:#f3e5f5
  style CF1 fill:#fce4ec
  style ET1 fill:#fce4ec
  style S1 fill:#e0f2f1
  style S2 fill:#e0f2f1
  style S3 fill:#e0f2f1
```

---

## 🔄 Data Flow: Quick Start Scenario

```mermaid
sequenceDiagram
  autonumber
  
  participant User as 👤 User
  interface as 💬 Interface
  QS as ⚡ quick_start
  DP as 🔍 discover_project
  MS as 💾 manage_status
  WD as 📝 write_module_doc
  FS as 📁 File System

  User->>interface: "Help me understand this project"
  interface->>QS: Trigger quick_start skill
  
  QS->>DP: Execute project discovery
  DP->>FS: Read package.json, tsconfig, etc.
  FS-->>DP: Config files content
  DP->>FS: Glob for entry points
  FS-->>DP: main.ts, router/index.ts
  DP-->>QS: project_map (87 files, frontend-react)
  
  QS->>MS: Initialize status
  MS->>FS: Create .ai/status.yaml
  MS-->>QS: Status initialized (phase: skeleton)
  
  QS->>WD: Generate tech overview skeleton
  WD->>FS: Create docs/project/tech-overview.md
  WD-->>QS: Skeleton with anchors
  
  QS-->>interface: ✅ Quick start complete!
  interface-->>User: 📚 View generated documents
  
  Note over User,FS: Total time: ~3s, Tokens: ~6,000
```

---

## 🎯 Skill Composition Patterns

```mermaid
flowchart LR
  subgraph Sequential["Sequential Composition"]
    S1["discover_project"] --> S2["manage_status"]
    S2 --> S3["write_module_doc"]
  end

  subgraph Conditional["Conditional Composition"]
    C1{Check Request Type}
    C1 -->|"anchor update"| C2["rewrite_anchor"]
    C1 -->|"continue"| C3["deep_analysis"]
    C1 -->|"refactor"| C4["patch_sections"]
  end

  subgraph Parallel["Parallel Optimization"]
    P1["read package.json"] 
    P2["read tsconfig.json"]
    P3["read vite.config.ts"]
    P1 & P2 & P3 -.-> P4["Merge results"]
  end

  style Sequential fill:#e8f5e9
  style Conditional fill:#fff3e0
  style Parallel fill:#e1f5ff
```

---

## 📊 Configuration System

```mermaid
pie showData
  title Configurable Parameters Distribution
  
  "Analysis Depth" : 25
  "Documentation Style" : 25
  "Performance Control" : 20
  "Teaching Strategy" : 20
  "Output Format" : 10
```

### Configuration Override Priority

```mermaid
flowchart TD
  A["🔧 System Defaults<br/>(skills/config.yaml)"] --> B["📝 User History<br/>(status.yaml)"]
  B --> C["🎯 User Request<br/>(HIGHEST PRIORITY)"]
  
  style A fill:#ffebee
  style B fill:#fff3e0
  style C fill:#e8f5e9
```

---

## 🔍 Execution Trace System

```mermaid
flowchart LR
  A["Skill Execution"] --> B["Trace Collection"]
  B --> C["Trace Storage<br/>.ai/traces/"]
  C --> D{Debug Mode?}
  D -->|"Yes"| E["Output Trace Report<br/>- Timeline<br/>- Token Usage<br/>- Confidence<br/>- Tool Calls"]
  D -->|"No"| F["Silent (No Trace)"]
  
  style A fill:#e1f5ff
  style B fill:#e8f5e9
  style C fill:#fff3e0
  style E fill:#f3e5f5
  style F fill:#ffebee
```

### Trace Metrics Dashboard

```mermaid
pie showData
  title Token Usage by Phase
  
  "Project Discovery" : 13.5
  "Symbol Analysis" : 46.2
  "Doc Generation" : 30.8
  "Status Management" : 9.5
```

---

## 🎓 Teaching Strategy Architecture

```mermaid
flowchart TD
  A["Complex Pattern Detected"] --> B["Few-shot Analogy<br/>10-15 lines simplified code"]
  A --> C["Counter-example<br/>Why Not analysis table"]
  A --> D["Confidence Annotation<br/>HIGH/MEDIUM/LOW"]
  
  B --> E["Map to Real Code<br/>Show enhancements"]
  C --> F["Trade-off Analysis<br/>Pros/Cons/Use Cases"]
  D --> G["Evidence & Reasoning<br/>Static vs Dynamic"]
  
  E & F & G --> H["Complete Module Doc<br/>Quality Check Passed"]
  
  style A fill:#f3e5f5
  style B fill:#e1f5ff
  style C fill:#fff3e0
  style D fill:#e8f5e9
  style H fill:#e0f2f1
```

---

## 📈 Performance Optimization

```mermaid
flowchart LR
  subgraph Before["Before Optimization"]
    B1["Serial execution<br/>All tools sequential"] --> B2["No cache<br/>Re-analyze every time"] --> B3["Fixed depth<br/>One size fits all"]
  end

  subgraph After["After Optimization"]
    A1["Parallel tools<br/>45% parallelization"] --> A2["Context cache<br/>44% token saved"] --> A3["Adaptive depth<br/>30% token saved"]
  end

  B1 -.-> A1
  B2 -.-> A2
  B3 -.-> A3

  style Before fill:#ffebee
  style After fill:#e8f5e9
```

---

## 🏆 Quality Assurance Pipeline

```mermaid
flowchart TD
  A["Generate Module Doc"] --> B{Quality Check}
  
  B -->|"Pass"| C["✅ Publish Document"]
  B -->|"Fail"| D["🔧 Auto-fix"]
  
  D --> E["Add missing confidence"]
  D --> F["Add few-shot analogy"]
  D --> G["Add counter-example"]
  D --> H["Add mermaid diagram"]
  D --> I["Split long code"]
  D --> J["Add practice tasks"]
  
  E & F & G & H & I & J --> K{Quality Check}
  
  K -->|"Pass"| C
  K -->|"Fail"| L["⚠️ Report Issues"]
  
  C --> M["💾 Update Status"]
  M --> N["🎉 Complete!"]
  
  style A fill:#e1f5ff
  style B fill:#fff3e0
  style C fill:#e8f5e9
  style D fill:#f3e5f5
  style L fill:#ffebee
  style N fill:#e0f2f1
```

---

<div align="center">

**Visual Architecture Guide - Project Learning Guide**

*Built with Mermaid diagrams for maximum clarity*

</div>
