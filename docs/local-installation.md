# Local installation record

Installed on 2026-09-21 for the operator's requested Codex-only deployment.

## Location and source

- Root: `/Users/oriens/Documents/workspaceX/Danus`
- Repository: https://github.com/frenzymath/Danus
- Branch: `codex`
- Upstream revision: `6d92e8d415933ca2ef52fd1a4da73fdfcd418f1c`
- Local macOS adjustments are committed on `codex`; nothing was pushed.
- New runtimes, caches, credentials, logs, and project data stay inside this tree.
  macOS system tools and the existing Chrome installation are reused.

## Runtime

| Component | Installed version / configuration |
|---|---|
| Platform | macOS Darwin 24.6.0, Apple Silicon arm64 |
| Python | 3.13.15, `runtime/python/`; virtual environment `runtime/venv/` |
| Node | 22.14.0, `runtime/node22/` |
| Codex CLI | 0.155.1, `runtime/codex-npm/` |
| Backend | Existing ChatGPT subscription; no API key configured |
| Codex state | `runtime/codex-home/`; private auth file, mode 0600 |
| Model | `gpt-5.6-sol`; main configuration uses ultra, verifier xhigh |
| LaTeX | Tectonic 0.17.0, `runtime/tex/bin/tectonic` |
| Verify service | `http://127.0.0.1:18091`, PID recorded in `runtime/run/verify.pid` |
| Dashboard | Port 18099 reserved; starts after a research project exists |

The default port 8091 was already occupied by an unrelated service. Both Danus
producer and consumer use the override in `config/danus.env`.

## Completed checks

- Bootstrap completed and an idempotent second run succeeded.
- `pip check`: no broken requirements.
- Codex login status: ChatGPT; a live model call returned `DANUS_CODEX_OK`.
- All three main MCP servers completed initialization and tool listing.
- The main gateway exposes no `fact_submit` tool.
- The verify HTTP health response identifies this deployment's process.
- A valid sample proof returned `correct` through the real HTTP/Codex pipeline.
- A false sample claim returned `wrong` with an explicit critical error and repair
  hints through that same pipeline. No research fact graph was created.
- Offline suite: **419 passed, 1 skipped**. The skipped zombie-state check requires
  `/bin/ps`, which this Codex execution sandbox forbids. Three upstream dependency
  or runpy warnings remain. The force-stop test verified actual SIGKILL termination.
- A minimal LaTeX document compiled to a nonempty PDF.
- `danus list --json` returned `[]`: no research project or worker has been started.

## Known limitation

The optional human-summary HTML-to-PDF path is **not verified working**. The
existing Chrome executable aborts with exit 134 in this execution environment,
even for `about:blank`; no diagnostic text identifies the cause. Markdown/KaTeX
dependencies are installed. The renderer now reports an error for failed or empty
output rather than falsely printing success. This does not affect the research
worker/verifier pipeline or the independently tested LaTeX engine.

The current sandbox also prevents signaling a service created by an earlier
shell invocation. Service status uses the HTTP identity check; a denied stop
keeps its PID file and reports the limitation. Normal local-terminal control is
available through the commands below. Services survive shell exit, but no
login-start or system-wide launch agent has been installed.

## Local adjustments

- Bootstrap selects the macOS Node archive and discovers the project-local
  Python when rebuilding. No Anthropic runtime dependency is installed.
- Service detachment uses Python's portable `start_new_session` support.
- Worker liveness recognizes macOS zombies when process-state inspection is
  permitted, with a conservative fallback when the sandbox denies it.
- Worker configuration disables inherited main-only authoring MCP servers.
  The verifier has equivalent local overrides in its ignored agent home.
- Authoring subprocesses exclude parent agent documents and inherited main MCP
  servers, preserving isolation when temporary files live inside this repository.
- Report rendering uses a dedicated temporary Chrome profile under `runtime/`.

## Commands (run from the repository root)

```bash
cd /Users/oriens/Documents/workspaceX/Danus
bash scripts/doctor.sh
bash scripts/check-codex.sh
bash scripts/services.sh status
./bin/danus list --json

# Start services again after a machine restart:
bash scripts/services.sh up verify

# Stop this installation's verifier from a local terminal:
bash scripts/services.sh down verify

# Launch the Codex main agent rooted in this repository:
./bin/codex

# Reproduce the offline checks without injecting deployment model overrides:
TMPDIR="$PWD/runtime/tmp" runtime/venv/bin/python -m pytest danus/ -q
```

Evidence is saved under `runtime/logs/`: `bootstrap.log`, `bootstrap-repeat.log`,
`doctor-final.log`, `pytest-final.log`, `mcp-smoke.json`, `codex-smoke.jsonl`,
`verify-smoke-result.json`, `verify-negative-smoke-result.json`, `tex-smoke.log`,
and `report-smoke.log`. Full verifier traces are in `runtime/verify-runs/`.
The installed Python package list is `runtime/installed-python.txt`.

## Awaiting the research problem

The operator requested installation first and research only after they describe
the problem. Their language, backend choice, and notification preference are
saved in `OPERATOR.md`. No research goal, worker swarm, dashboard, or recurring
monitor is active yet. At project start, establish the problem, target, stopping
condition, and worker roster; then create the project, start the swarm/dashboard,
and attach monitoring to the current Codex task. Notify only on meaningful
progress, completion, failure, or required user action.
