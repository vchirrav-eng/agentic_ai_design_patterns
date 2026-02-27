# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Overview

A collection of Jupyter notebooks demonstrating agentic AI design patterns using the Google Gemini API (`google-genai` SDK) with `gemini-2.5-flash` as the model. Each notebook is a self-contained, runnable implementation of one pattern.

## Setup

Install dependencies (varies by notebook — run the first cell):
```bash
pip install google-genai pydantic
```

Set the API key before running:
```python
import os
os.environ['GEMINI_API_KEY'] = 'your-actual-key-here'
```

Run notebooks in Jupyter or JupyterLab, or execute cell code directly as Python scripts.

## Architecture & Patterns

Every notebook follows the same structure:
1. `pip install` cell
2. API key setup cell
3. Agent implementation + orchestrator + execution block

All patterns use `genai.Client()` which auto-reads `GEMINI_API_KEY`. Structured outputs use Pydantic models with `response_mime_type='application/json'` and `response_schema=<PydanticModel>` in `config`.

### Pattern Catalog

| Notebook | Pattern | Core Mechanic |
|---|---|---|
| `Agentic_AI_Sequential_Pattern.ipynb` | Sequential | Fixed linear pipeline: A → B → C |
| `Agentic_AI_Loop_Pattern.ipynb` | Loop | Writer + Critic loop until approved or `max_iterations` |
| `Agentic_AI_Coordinator_Pattern.ipynb` | Coordinator | LLM routes to specialized subagent, then synthesizes result |
| `Agentic_AI_HITL_Pattern.ipynb` | Human-in-the-Loop | Agent produces draft → console `input()` approval gate → refine or release |
| `Agentic_AI_Parallel_Pattern.ipynb` | Parallel | `concurrent.futures.ThreadPoolExecutor` fans out to agents concurrently, then synthesizes |
| `Agentic_AI_ReAct_Pattern.ipynb` | ReAct | Reason → Act → Observe loop with tool dispatch and persistent `memory_context` string |
| `Agentic_AI_Hierarchical_Pattern.ipynb` | Hierarchical | Root coordinator decomposes task → mid-level coordinators → worker agents |
| `Agentic_AI_Iterative_Refinement_Pattern.ipynb` | Iterative Refinement | Generator + Evaluator + Prompt Enhancer loop (prompt itself is improved each cycle) |
| `Agentic_AI_Swarm_Pattern.ipynb` | Swarm | Dispatcher routes to first node; agents autonomously hand off via `next_agent` field in shared history |
| `Agentic_AO_Review_Critic_Pattern.ipynb` | Review & Critic | Generator + security-critic loop; critic returns structured `quality_score` and `meets_requirements` |

### Key Design Conventions

- **Exit conditions**: Every loop-based pattern has two exits: a success condition (approval/consensus) and `max_iterations` to prevent infinite loops.
- **Structured routing**: Coordinator and Swarm patterns use Pydantic `Enum` fields for type-safe routing decisions.
- **Low temperature for critics**: Evaluator/critic agents use `temperature: 0.1` for deterministic grading; creative agents use higher values (0.4+).
- **Memory as string**: ReAct and Swarm patterns accumulate shared context in a plain string appended each iteration, passed back into the prompt.
