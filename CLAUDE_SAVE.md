# AlchemyTools — Claude Save Protocol

> When the user says **"Claude save this tool"** (or "add this to AlchemyTools", "save this to the library"),
> follow this protocol exactly. Read this file first, then execute all steps.

---

## Step 0: Reusability Gate

Before saving, verify the code passes ALL of these:

- [ ] **Generic** — not hardcoded to one project (no AlchemyGoldOS-specific logic baked in)
- [ ] **Single responsibility** — does one thing clearly
- [ ] **< 300 lines** — if larger, split into sub-tools or save only the core function
- [ ] **Stateless OR clearly bounded state** — easy to drop in without side effects
- [ ] **Not test-only code** — tests describe usage, they don't get saved as tools

If it fails the gate, tell the user why and suggest what to extract instead.

---

## Step 1: Read the Code

Read the target file or function. Understand:
- What it does (one sentence)
- What it takes as input / returns as output
- What it currently imports from the project
- Any hardcoded values (URLs, tokens, file paths, project names)

---

## Step 2: Refactor for Modularity

Apply these rules before saving:

**Remove project coupling:**
- Replace project-specific imports with standard library equivalents or function parameters
- No `from gold.*`, `from alchemy.*`, or similar internal paths
- No hardcoded project names, tokens, API keys, or file paths → make them **parameters with sensible defaults**

**Add types:**
- Python: add type hints to all function signatures
- TypeScript: ensure all params and returns are typed

**Logging:**
- Replace internal logger calls with `logging.getLogger(__name__)` (Python) or `console` (TS)
- Keep logging minimal — one `logger.debug` per significant step

**Dependencies:**
- Only standard library + widely-available packages (requests, httpx, pydantic, etc.)
- List any non-standard deps in `meta.json` `dependencies` field

**Do NOT:**
- Add features the original code doesn't have
- Abstract for hypothetical future use cases
- Add error handling for cases that can't happen in a standalone context

---

## Step 3: Categorize

Choose ONE category folder:

### Python (`python/`)
| Folder | When to use |
|--------|-------------|
| `api/` | HTTP clients, request helpers, retry logic, webhook handlers |
| `auth/` | JWT, OAuth, API key validation, session handling |
| `data/` | Parsers, validators, transformers, serializers, formatters |
| `llm/` | Prompt builders, chain patterns, token counting, model helpers |
| `patterns/` | Design patterns, decorators, async helpers, event systems |
| `utils/` | String, file, time, math, sorting, hashing — general purpose |

### TypeScript (`typescript/`)
| Folder | When to use |
|--------|-------------|
| `api/` | Fetch wrappers, WebSocket helpers, SSE, streaming |
| `components/` | React UI components |
| `hooks/` | React hooks |
| `patterns/` | State patterns, reducers, event buses |
| `utils/` | General utilities — string, file, time, format |

**Naming:** `tool-name-kebab-case` — short, descriptive, noun or verb-noun.

---

## Step 4: Create the Tool Folder

Path: `C:/Users/info/GitHub/AlchemyTools/{language}/{category}/{tool-name}/`

Create exactly **3 files**:

### `tool.py` or `tool.ts`
The refactored, standalone code. Must have:
- File-level docstring/comment (what it does, 1-3 sentences)
- Clean imports (standard lib only or listed deps)
- All function signatures typed
- No project-specific references

### `README.md`
```markdown
# {Tool Display Name}

{One paragraph description.}

## Usage

\`\`\`python
# Minimal working example
from tool import YourClass

result = YourClass().method(input="value")
print(result)
\`\`\`

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| param1    | str  | —       | What it does |
| param2    | int  | 5       | Optional setting |

## Returns

`ReturnType` — description of what comes back.

## Dependencies

None — standard library only.
(OR: `pip install httpx` / `npm install axios`)

## Source

Origin repo: `NeoSynaptics/{RepoName}`
File: `path/to/original/file.py`
Extracted: {YYYY-MM-DD}
```

### `meta.json`
```json
{
  "name": "tool-name-kebab-case",
  "display_name": "Human Readable Name",
  "description": "One sentence. What it does and why you'd use it.",
  "language": "python",
  "category": "utils",
  "tags": ["relevant", "search", "terms"],
  "origin_repo": "NeoSynaptics/AlchemyGoldOS",
  "origin_file": "gold/path/to/original.py",
  "created": "YYYY-MM-DD",
  "version": "1.0.0",
  "dependencies": []
}
```

---

## Step 5: Update `index.json`

Read `C:/Users/info/GitHub/AlchemyTools/index.json`.

Add an entry to the `tools` array:
```json
{
  "name": "tool-name-kebab-case",
  "display_name": "Human Readable Name",
  "description": "One sentence.",
  "language": "python",
  "category": "utils",
  "path": "python/utils/tool-name-kebab-case",
  "tags": ["tag1", "tag2"],
  "created": "YYYY-MM-DD"
}
```

---

## Step 6: Commit to GitHub

```bash
cd C:/Users/info/GitHub/AlchemyTools
git add {tool-folder-path}/
git add index.json
git commit -m "add: {tool-name} ({language}/{category})"
git push origin main
```

---

## Example

User says: *"Claude save this tool"* — points to `gold/hybrid/retry.py`

Claude:
1. Reads the file → `retry_with_backoff(fn, max_retries=3, base_delay=1.0)` — clean, generic
2. Reusability check: passes all 4 gates
3. Refactors: removes `gold.hybrid` import, adds type hints, parameterizes delay
4. Category: `python/patterns/` (it's a retry decorator pattern)
5. Creates: `python/patterns/retry-with-backoff/` with tool.py + README.md + meta.json
6. Updates index.json
7. Commits: `add: retry-with-backoff (python/patterns)`

---

## Quick Reference Card

```
"Claude save this tool"
     ↓
1. Read target code
2. Reusability gate (4 checks)
3. Refactor (strip coupling, add types, parameterize)
4. Pick category (python/ or typescript/ → subcategory)
5. Create folder: {language}/{category}/{name}/
   ├── tool.py / tool.ts
   ├── README.md
   └── meta.json
6. Update index.json (add entry to tools[])
7. git add → commit → push
```
