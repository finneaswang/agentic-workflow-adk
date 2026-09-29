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
- [ ] Step 3 · Monolithic agent — first function tool
- [ ] Step 4 · Fan-out and the human pause — `Workflow`, `JoinNode`, `RequestInput`
- [ ] Step 5 · State and Router — `Event(state=...)`, deterministic policy router
- [ ] Step 6 · Memory Bank — GEAP memory via callbacks
- [ ] Step 7 · RAG Engine — audience comments as one more reader in the fan-out
- [ ] Step 8 · The video — `LongRunningFunctionTool` + Veo
- [ ] Step 9 · Deploy — the finished app on Cloud Run
- [ ] Step 10 · Summary

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

_(filled in as the steps land)_
