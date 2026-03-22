# Module Dependency Graph

```mermaid
flowchart LR

    subgraph entry["Entry Point"]
        main["__main__"]
    end

    subgraph core["Core Modules"]
        cli["cli"]
        app["app"]
        notes["notes"]
    end

    subgraph tests["Tests"]
        test_app["test_app"]
        test_cli["test_cli"]
        test_notes["test_notes"]
    end

    subgraph scripts["Scripts"]
        serve_docs["serve_docs"]
    end

    %% Entry point
    main -->|"import cli"| cli

    %% CLI hub
    cli -->|"import configure_logging"| app
    cli -->|"import create_note"| notes

    %% Test imports
    test_app -.->|"import console_format, main"| app
    test_cli -.->|"import cli"| cli
    test_notes -.->|"import build_note_path, create_note, slugify"| notes

    %% Styles
    classDef entryStyle fill:#4a90d9,stroke:#2c5f8a,color:#fff
    classDef coreStyle fill:#50b86c,stroke:#2d7a42,color:#fff
    classDef leafStyle fill:#8bc34a,stroke:#5a8a2a,color:#fff
    classDef testStyle fill:#9e9e9e,stroke:#616161,color:#fff
    classDef scriptStyle fill:#ff9800,stroke:#e65100,color:#fff

    class main entryStyle
    class cli coreStyle
    class app,notes leafStyle
    class test_app,test_cli,test_notes testStyle
    class serve_docs scriptStyle
```

## Key

| Style | Meaning |
|-------|---------|
| Blue | Entry point |
| Green | Core hub module (imports other internal modules) |
| Light green | Leaf module (no internal imports — safe to change in isolation) |
| Grey | Test modules |
| Orange | Standalone scripts |
| Solid arrow | Runtime dependency |
| Dashed arrow | Test dependency |

## Circular Dependencies

**None detected.** The dependency graph is a clean DAG (directed acyclic graph).

## Safe-to-Change Analysis

| Module | Risk | Why |
|--------|------|-----|
| `notes.py` | Low | Leaf module. Only `cli.py` and `test_notes.py` depend on it. |
| `app.py` | Low | Leaf module. Only `cli.py` and `test_app.py` depend on it. |
| `cli.py` | Medium | Hub module. `__main__.py` and `test_cli.py` depend on it. Changes to its public API affect the entry point. |
| `__main__.py` | Low | Only imports `cli`. No other module depends on it. |
| `serve_docs.py` | Low | Fully standalone. No internal imports at all. |
