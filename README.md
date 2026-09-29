# Agentic Workflow with ADK — VibeStudio

My completed build of **VibeStudio**, a hands-on workshop codelab from Google
([gca-americas/vibetube-studio](https://github.com/gca-americas/vibetube-studio)).
Over ten steps you turn a human-run short-video channel into an agentic
pipeline built on the **Agent Development Kit (ADK)**: the research runs in
parallel, a human picks the direction, a deterministic router enforces policy,
and the video render happens as a long-running async tool.

> The nine lab-edit files (`agent/graph.py`, `agent/deliver.py`, `stage0..6/agent.py`)
> are the work itself — upstream gitignores them so half-finished edits can't be
> committed; this fork intentionally tracks them, starting from the carved
> TODO state and filling in all 17 hands-on edits step by step.

## Progress

- [x] Environment setup (`setup_project.sh` + `setup_codelab.sh`, preflight green)
- [x] Step 3 · Monolithic agent — first function tool
- [x] Step 4 · Fan-out and the human pause — `Workflow`, `JoinNode`, `RequestInput`
- [x] Step 5 · State and Router — `Event(state=...)`, deterministic policy router
- [x] Step 6 · Memory Bank — GEAP memory via callbacks
- [x] Step 7 · RAG Engine — audience comments as one more reader in the fan-out
- [x] Step 8 · The video — `LongRunningFunctionTool` + Veo
- [x] Step 9 · Deploy — the finished app on Cloud Run
- [ ] Step 10 · Summary

**Live deployment**: [vibestudio-851240471506.us-central1.run.app](https://vibestudio-851240471506.us-central1.run.app)
(the full pipeline app of step 9, running on Cloud Run in `us-central1`).

Publishing to the workshop platform (vibetube.dev, event `UBC`) is left off
for now: the app renders with the free Veo stand-in (`STUDIO_REAL_VIDEO=0`).
To publish for real, set `STUDIO_REAL_VIDEO=1` in `.env`, re-run the pipeline,
and publish from the app — vibetube.dev keeps one video per project + room.

## How to run

```bash
# a Google Cloud project with billing recorded in ~/project_id.txt
./setup_project.sh        # creates/links project + billing
./setup_codelab.sh        # uv deps, APIs, .env, workbench on :4600
```

Then open <http://localhost:4600>. `scripts/stop.sh` / `scripts/start.sh`
manage the workbench; `python scripts/preflight.py` re-checks the environment.

Development notes: video renders use the free stand-in
(`STUDIO_REAL_VIDEO=0`) while building; the final publish switches to real Veo.

## What's in here

Same layout as upstream — see the
[upstream README](https://github.com/gca-americas/vibetube-studio#repository-layout)
for the full map. The short version:

```
agent/          the workflow graph under development (graph.py, desk.py, ...)
stage0..6/      per-step sandboxes, one root_agent each
vibestudio/     the completed standalone app (deployed in step 9)
server/ web/    the VibeStudio workbench UI (port 4600)
checks/         hole registry + verifiers
starter/        the carved files students receive
```

## What I learned

_(being filled in as the steps land)_

- **Step 3 · Monolithic agent**: tools are just Python functions the model calls
  through a five-stage protocol (`function_call` → runtime executes →
  `function_response` → synthesis). But prompt rules are advisory — a
  follow-up message made the agent skip its own "confirm before scripting"
  rule. That gap is why graphs exist.
- **Step 4 · Fan-out + human pause**: a `Workflow` is a first-class agent.
  Two reader chains from `START` into a `JoinNode`; an `Agent` itself is a
  node (`single_turn`, `output_schema=Directions`); a human pause is a node
  that yields `RequestInput` — the run ends, the session holds a pending
  receipt, and a `function_response` with the interrupt id resumes it.
- **Step 5 · State and Router**: shared state via `Event(state=...)` with the
  `user:` prefix for durable prefs; parameters bound from state by name.
  Policy is deterministic code reading data (`policy_words.txt`) — the model
  never grades its own homework. `mode="task"` agents run tools to a
  `finish_task` with a typed schema (the quarantine cleaner).
- **Step 6 · Memory Bank**: `before_model_callback` injects recalled facts
  into the outgoing `LlmRequest`; `after_agent_callback` submits the turn for
  consolidation. Facts carry topics (CREATOR_TASTE, CHANNEL_RULES) and are
  deduped/updated by the service, not by prompts.
- **Step 7 · RAG Engine**: audience comments live in a GEAP corpus; a
  retrieval function node is just one more reader in the research fan-out,
  returning cited passages into the join dict.
- **Step 8 · Long-running tool**: the render desk wraps Veo in a
  `LongRunningFunctionTool` — the graph suspends with a pending receipt
  (`render_submit`), a separate worker process (`agent/deliver.py`) resumes
  it by call id. Nothing polls; nothing stays alive waiting.
