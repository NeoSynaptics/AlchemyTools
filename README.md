# AlchemyTools

Reusable code extracted from NeoSynaptics projects. Each tool is standalone, typed, and production-tested.

## Usage

Say **"Claude save this tool"** — Claude reads [CLAUDE_SAVE.md](CLAUDE_SAVE.md) and handles extraction, refactoring, and commit automatically.

## Library

<!-- AUTO-UPDATED by index.json -->

### Python

| Tool | Category | Description |
|------|----------|-------------|
| *(empty — first tool goes here)* | | |

### TypeScript

| Tool | Category | Description |
|------|----------|-------------|
| *(empty — first tool goes here)* | | |

## Structure

```
AlchemyTools/
├── CLAUDE_SAVE.md          ← Claude reads this when saving a tool
├── README.md               ← This file
├── index.json              ← Machine-readable index
├── python/
│   ├── api/
│   ├── auth/
│   ├── data/
│   ├── llm/
│   ├── patterns/
│   └── utils/
└── typescript/
    ├── api/
    ├── components/
    ├── hooks/
    ├── patterns/
    └── utils/
```

Each tool lives in its own folder:
```
python/utils/my-tool/
├── tool.py        ← standalone, typed code
├── README.md      ← usage example + parameters
└── meta.json      ← metadata (tags, origin, version)
```

## Auto-Discovery

AlchemyKernel scans registered repos every hour and surfaces reusable candidates in AlchemyPull (teal cards in the Insight Feed).
