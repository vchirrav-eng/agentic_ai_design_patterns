# Agentic AI Design Patterns

A collection of practical, runnable Jupyter notebooks demonstrating common **single-agent** and **multi-agent** design patterns using the **Google Gemini API** (`google-genai` SDK) with `gemini-2.5-flash`.

Each notebook is fully self-contained and implements one pattern end-to-end with a concrete working example.

---

## Patterns

### Single-Agent Patterns

| Pattern | Notebook | Description |
|---|---|---|
| **Sequential** | `Agentic_AI_Sequential_Pattern.ipynb` | A fixed linear pipeline where each agent's output feeds directly into the next. No dynamic routing — pure A → B → C chaining. |
| **Loop** | `Agentic_AI_Loop_Pattern.ipynb` | A writer agent produces output; a critic agent evaluates it using structured outputs (Pass/Fail). The loop continues until approval or `max_iterations`. |
| **ReAct** | `Agentic_AI_ReAct_Pattern.ipynb` | Reason → Act → Observe loop. The agent selects and executes tools from a registry, accumulating observations in a memory context string each iteration. |
| **Human-in-the-Loop (HITL)** | `Agentic_AI_HITL_Pattern.ipynb` | Agent produces a draft and pauses for human review via console input. On rejection, the human provides feedback that is fed back into the next agent cycle. |
| **Iterative Refinement** | `Agentic_AI_Iterative_Refinement_Pattern.ipynb` | A generator, evaluator, and prompt-enhancer work in a loop. Unlike a simple loop pattern, the *prompt itself* is rewritten each cycle by the enhancer to address the evaluator's feedback. |

### Multi-Agent Patterns

| Pattern | Notebook | Description |
|---|---|---|
| **Parallel** | `Agentic_AI_Parallel_Pattern.ipynb` | An orchestrator fans out the same input to multiple specialist agents concurrently using `concurrent.futures.ThreadPoolExecutor`, then a synthesizer merges all results. |
| **Coordinator** | `Agentic_AI_Coordinator_Pattern.ipynb` | A central coordinator uses an LLM to classify the user's intent (structured output with enum routing), dispatches to the matching specialist agent, then synthesizes a final user-facing response. |
| **Hierarchical** | `Agentic_AI_Hierarchical_Pattern.ipynb` | Three-level hierarchy: a root coordinator decomposes an ambiguous task into directives, mid-level coordinators manage sub-domains, and worker agents perform the actual execution. |
| **Swarm** | `Agentic_AI_Swarm_Pattern.ipynb` | A dispatcher routes to the first agent; each agent contributes to a shared history and autonomously selects the next agent via a `next_agent` handoff field. Terminates on consensus or `max_iterations`. |
| **Review & Critic** | `Agentic_AO_Review_Critic_Pattern.ipynb` | A generator produces output; a critic agent acts as a domain expert (e.g., security auditor) returning a structured `quality_score` and approval flag. Rejected output loops back to the generator with feedback. |

---

## Setup

**Install dependencies:**
```bash
pip install google-genai pydantic
```

**Set your Gemini API key** (replace the placeholder in each notebook's second cell):
```python
import os
os.environ['GEMINI_API_KEY'] = 'your-api-key-here'
```

Get a free API key at [Google AI Studio](https://aistudio.google.com/).

---

## Key Design Principles Demonstrated

- **Structured outputs**: Pydantic models with `response_mime_type='application/json'` and `response_schema` for type-safe agent decisions and routing.
- **Bounded loops**: Every iterative pattern includes a `max_iterations` guard to prevent infinite execution.
- **Low temperature for evaluators**: Critic/evaluator agents use `temperature=0.1` for deterministic grading; creative agents use higher values.
- **Shared memory as string**: ReAct and Swarm patterns pass accumulated context as a growing string appended to each prompt.
