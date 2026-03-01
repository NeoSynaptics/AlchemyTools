# Task Decomposer

Standalone task decomposition engine that breaks high-level task descriptions or PRDs into ordered, dependency-aware, atomic subtasks. Works with any Ollama-compatible or OpenAI-compatible LLM API. Zero project dependencies — pure Python + httpx.

## Usage

```python
from tool import TaskDecomposer, ollama_backend

# Create with Ollama
decomposer = TaskDecomposer(llm=ollama_backend("qwen2.5-coder:14b"))
result = await decomposer.decompose("Build JWT auth with login, register, and password reset")

for task in result.tasks:
    print(f"{task.id}: {task.title} (complexity: {task.complexity}, deps: {task.dependencies})")

# Or with OpenAI
from tool import openai_backend
decomposer = TaskDecomposer(llm=openai_backend("gpt-4", api_key="sk-..."))

# Parse a full PRD
result = await decomposer.decompose_prd(open("prd.md").read())
for phase in result.phases:
    print(f"\n{phase.name}: tasks {phase.task_ids}")

# Validate dependencies (cycle detection)
valid, errors = decomposer.validate(result.tasks)
```

## Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| llm | LLMBackend | — | Async callable (system, user) → text |
| max_subtasks | int | 50 | Hard cap on subtasks per decomposition |

## Returns

`DecompositionOutput` with fields:
- `summary` — 1-2 sentence plan summary
- `tasks` — ordered list of `DecomposedTask` objects
- `effort` — estimated effort (small/medium/large/xlarge)
- `phases` — named groups of task IDs (PRD mode only)
- `dependency_order` — topologically sorted task IDs
- `warnings` — cycle detections, parse errors, etc.

## Dependencies

`pip install httpx`

## Source

Origin repo: `NeoSynaptics/AlchemyGoldOS`
File: `gold/tasks/decomposer.py`
Extracted: 2026-03-01
