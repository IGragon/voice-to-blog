# Voice-to-Blog

> A personalized, voice-first content studio with human approval and safe, agent-driven publishing operations.

Voice-to-Blog turns spoken ideas into polished, style-aware content and helps manage the workflow around it. A user can record or upload an idea, review an AI-generated article and its Telegram adaptation, and approve a publishing plan. An operations agent then executes that plan inside a simulated content-management environment where scheduling conflicts, failures, and safety rules can be tested without risking real accounts.

The project is being developed for a Machine Learning System Design university course, with the intention of growing into a useful real-world product.

## Why this project?

Content creation involves more than generating text. Creators must preserve their voice, revise drafts, adapt content for different platforms, coordinate schedules, and verify that publishing actions succeeded.

Voice-to-Blog combines two related capabilities:

- **Content intelligence:** transcription, idea extraction, personalized drafting, revision, and preference learning.
- **Content operations:** planning, scheduling, publishing, validation, failure recovery, and lifecycle management through tools.

The result is a stateful, human-in-the-loop system that must achieve goals safely—not just produce plausible prose.

## Intended workflow

1. Capture an idea through voice, text, and eventually supporting notes or images.
2. Transcribe and structure the raw input.
3. Retrieve the user's writing preferences and accepted examples.
4. Generate and review a personalized long-form draft.
5. Let the user edit, reject, revise, or approve the content.
6. Create a Telegram version and a publishing plan.
7. Execute approved actions in a simulated CMS.
8. Inspect the resulting state, failures, costs, and execution trace.
9. Learn reversible editorial preferences from user feedback.

Nothing may be published without explicit approval.

## System design

The initial architecture has two logical agents coordinated by an explicit workflow controller:

- **Editorial Agent** — processes transcripts, extracts ideas, applies style preferences, drafts content, revises it, and evaluates writing quality.
- **Operations Agent** — inspects the content calendar, selects tools, resolves scheduling constraints, executes approved actions, and recovers from simulated failures.
- **Workflow Controller** — owns persistent state, approval gates, transitions, resumability, and execution history.

Drafts are stored as versioned content artifacts with metadata rather than passed around as unstructured chat messages.

## Simulated publishing environment

A lightweight CMS and social-publishing simulator backed by SQLite provides a safe, reproducible environment for agent evaluation. Planned tools include:

- `get_content_calendar`
- `create_post`
- `update_post`
- `schedule_post`
- `publish_post`
- `cancel_post`
- `get_post_status`
- `get_post_metrics`

The simulator enforces business rules such as approval requirements, duplicate-publication prevention, valid state transitions, and schedule constraints. It will also support controlled failure injection, including occupied time slots, ambiguous timeouts, duplicate scheduling, changed dependencies, and accidental overwrites.

Simulated engagement metrics are test signals for agent behavior; they are not evidence of real-world content performance.

## Personalization

The first personalization system will combine:

- an editable, structured writing-style profile;
- retrieval of previously accepted writing examples;
- preferences inferred from the user's revisions;
- versioned and reversible preference updates.

The project will compare three drafting strategies:

1. A generic LLM prompt.
2. A static style profile with examples.
3. An adaptive profile updated from editing feedback.

Fine-tuning is out of scope until enough meaningful feedback data exists to evaluate it responsibly.

## Research questions

The academic evaluation primarily focuses on operational reliability, with personalization as a
secondary research direction:

| Question | Evaluation | Key metrics |
| --- | --- | --- |
| Can an agent safely complete multi-step content operations? | Fixed workflow vs. tool-using agent | Task completion, invalid actions, approval violations, recovery rate |
| Does personalization reduce manual editing? | Generic vs. static vs. adaptive drafting | Editing time, edit distance, human preference |
| What does agentic behavior cost? | Compare configurations on the same workload | Cost per successful task, latency, tool calls, failures |

Approval violations are hard failures, regardless of whether the requested task is otherwise completed.

## MVP

The first end-to-end demonstration will support one content workflow:

- record or upload voice;
- transcribe it locally;
- create a style-aware blog draft;
- edit and explicitly approve the draft;
- produce a Telegram adaptation;
- schedule both pieces in the simulated CMS;
- inspect the content calendar and complete action trace.

The evaluation suite will use reproducible scenarios with expected final CMS states, including conflicts and injected failures.

## Proposed stack

| Area | Technology |
| --- | --- |
| User interface | Streamlit |
| Speech-to-text | `faster-whisper` |
| Language model | Hosted LLM with structured output and tool calling |
| Workflow orchestration | LangGraph or OpenAI Agents SDK (one will be selected) |
| Persistence and simulator | SQLite |
| Evaluation | Pytest and JSON scenarios |
| Observability | Structured logs, execution traces, latency and cost tracking |

## Development

The project targets Python 3.12 and uses `uv` for dependency management.

```bash
uv sync --dev
uv run pre-commit install
```

Run the quality checks locally with:

```bash
uv run ruff format --check .
uv run ruff check .
uv run pre-commit run --all-files
```

Once application tests are present, run them with `uv run pytest`.

Apply automatic formatting and safe lint fixes with:

```bash
uv run ruff format .
uv run ruff check --fix .
```

## Project status

This repository is at the initial design and implementation stage. The immediate goal is a narrow but complete vertical slice before adding broader platform support, image input, richer preference learning, or real publishing integrations.

The course-facing first-iteration design is documented in
[`docs/ml_system_design_doc.md`](docs/ml_system_design_doc.md).

Real external publishing is intentionally outside the MVP. The simulator is the controlled environment for studying safety, reliability, recovery, and cost while the content-creation pipeline remains genuinely useful.
