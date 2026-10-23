# Hinoki user guide

Hinoki is a desktop IDE built around KannaCode, an AI coding agent. This guide covers every part of it.

**KannaCode can do almost everything in this guide for you.** Ask it in the Chat panel or a KannaCode terminal:
"set up a local model", "connect to my GPU server", "install the Rust extension", "what should I do next?". It
operates Hinoki through its built-in tools (they're listed in the table below), and any change asks for your
approval first.

| Ask KannaCode to… | Tools it uses |
|---|---|
| Check your setup and suggest improvements | `hinoki_status`, `suggest_next_steps` |
| Create or open a project, open files | `create_project`, `open_folder`, `open_file` |
| Run terminals, tasks, any IDE command | `open_terminal`, `run_task`, `run_ide_command` |
| Find and fix problems | `get_problems`, `get_editor_state` |
| Change settings, web search | `list_settings`, `set_setting`, `set_web_search` |
| Manage extensions | `search_extensions`, `install_extension` |
| Run models locally or on a GPU server | `add_server`, `start_server`, `use_server_with_kannacode` |
| Add cloud models and keys | `add_model`, `set_api_key`, `set_default_model` |
| Add MCP servers | `add_mcp_server` |
| Set up SSH and remote development | `add_ssh_host`, `check_ssh_host`, `connect_ssh` |

## The window

- **Left activity bar**, top to bottom: Explorer, KannaCode (the KannaCode view: install state, model, update, open
  a KannaCode terminal), Models & Servers, Benchmarks (bar-chart icon: live run and leaderboard), Search, Source
  Control, Run and Debug, Extensions, Testing, Containers, plus extension views (Python, Jupyter). Settings (gear)
  at the bottom.
- **Right side:** the AI Chat panel (KannaCode chat).
- **Status bar** (bottom): Local or the SSH host (click: workspace mode / connect / disconnect), the KannaCode model
  (click: switch), git branch, problems count.
- **Command palette:** Cmd/Ctrl+Shift+P. Hinoki commands are under "Hinoki" and "KannaCode".

## Projects (PyCharm-style)

- **Projects home** (the start page whenever no project is open; File → Projects…): New Project, Open, Clone,
  Remote, and recent projects with search, git branch and type; hover a row to open it in a new window or forget it.
- **New Project** (File → New Project…, command `hinoki.projects.new`): templates Python (uv app + .venv),
  Python package (src layout + pytest), FastAPI (+ uvicorn run configuration), Data science (Jupyter, pandas,
  matplotlib, starter notebook), Node.js + TypeScript, Empty, and "Describe it to KannaCode" (creates the folder,
  opens it and hands the description to KannaCode in the Chat panel). Name, location (`hinoki.projects.directory`,
  default ~/PycharmProjects), Python version (uv downloads missing ones), git, new window.
- **Clone Repository…** (File menu, `hinoki.projects.clone`), **Close Project** (back to the Projects home).
- The file tree is the **Project** view (first icon on the left).

## Where things live

| Path | What |
|---|---|
| `~/.kanna/models.json` | KannaCode model aliases (provider, model_id, base_url/host, context, max_tokens, effort and tool settings) |
| `~/.kanna/settings.json` | KannaCode settings: `model` (default alias), `mode`, `effort`, `search_mode` (written by Hinoki's web search setting) |
| `~/.kanna/keys.json` | API keys per alias, mode 0600: a literal or `{"env": "VAR"}` |
| `~/.kanna/mcp.json` | KannaCode MCP servers (Hinoki registers itself as `hinoki`); a project's `.kanna/mcp.json` adds per-project ones |
| `~/.kanna/skills/` | KannaCode skills, including this one (`hinoki/SKILL.md`) |
| `~/.hinoki/kannacode/` | the private KannaCode install Hinoki manages (venv; `bin/kannacode`) |
| `~/.hinoki/servers.json`, `~/.hinoki/servers/<id>.log` | model servers in Models & Servers, and their logs |
| `~/.hinoki/settings.json` | Hinoki (IDE) user settings; a project's `.theia/settings.json` holds workspace settings |
| `~/.hinoki/control.json` | the local control endpoint these tools use (token, 0600) |
| `~/.ssh/config` | SSH host aliases Hinoki offers |

## First-run setup wizard

The command "Setup (KannaCode and Models)". Three steps: (1) KannaCode: installs the bundled
copy privately into `~/.hinoki/kannacode` with uv, no PATH or system Python changes; (2) Model: detects hardware and
offers a local model (installs the server, downloads, starts, registers, tests), a cloud provider (key + test), an
SSH GPU host, or any OpenAI-compatible endpoint; (3) Ready. With `hinoki.setup.completed` false it opens at launch.

## KannaCode in Hinoki

- **KannaCode view and terminals:** "Open KannaCode in Terminal" runs `kannacode` in the project with
  `hinoki.kannacode.model` (empty: your default) and `hinoki.kannacode.mode`; `hinoki.kannacode.extraArgs` adds
  flags; `hinoki.kannacode.persistentSession` (auto/always/never) keeps sessions alive in tmux across window
  closes and dropped SSH connections.
- **Chat panel:** the KannaCode chat agent; modes auto-edit (default), ask, auto and plan; tool calls, diffs,
  approvals and questions show inline; each chat keeps its own KannaCode session.
- **Inline AI and code completion** use KannaCode's chosen model (Hinoki syncs `models.json` into the IDE's AI
  settings).
- **Status bar model** / command "KannaCode: Select Model" switches the model.
- **Web search** (`hinoki.kannacode.webSearch`, default `on`): on = search freely; ask = approve each search;
  docs = documentation sites only; off. Hinoki writes it to `~/.kanna/settings.json`, so it applies to every
  KannaCode session on the machine. Or ask KannaCode to change it.
- **Updates:** "KannaCode: Check for Updates" (daily automatic check when `hinoki.kannacode.checkForUpdates`).
  The channel `hinoki.kannacode.updateUrl` is a `latest.json` URL, file or folder: `{"version", "wheel",
  "sha256", "notes"}`; Hinoki downloads, verifies and installs the wheel. "Install / Update KannaCode" reinstalls.
  `hinoki.kannacode.path` points Hinoki at another kannacode executable.
- **Keys:** "KannaCode: Set API Key for a Model" or `set_api_key`.

## Models & Servers (local model servers)

The panel has three tabs: **Servers** (server kinds with Install, and configured servers with Start/Stop, log,
"Use with KannaCode", edit), **APIs** (cloud providers and endpoints), **Remote** (SSH hosts: check, set up a server
there, open a remote workspace).

Hinoki starts servers as detached background processes (wrapped in `caffeinate -i -s` on macOS so the Mac does not
sleep mid-run). They keep running after Hinoki closes and hold their memory until stopped.

| Kind | Runs on | Default port | `model` is | Install |
|---|---|---|---|---|
| mlx-serve | Apple silicon | 11234 | HF repo or local MLX folder | `brew tap ddalcu/mlx-serve … && brew install mlx-serve` |
| mlx-vlm | Apple silicon | 8081 | HF repo or local folder (vision-language) | `uv tool install mlx-vlm` |
| mlx-lm | Apple silicon | 8080 | HF repo or local folder | `uv tool install mlx-lm` |
| vllm | Linux + NVIDIA (Windows via WSL) | 8000 | HF repo or local folder | `uv tool install vllm` |
| llama.cpp | everywhere | 8090 | a .gguf file | brew / winget |
| ollama | everywhere | 11434 | an Ollama tag (local or `:cloud`) | brew / install script / winget |
| custom | anything | 9000 | whatever the command expects | — |

### Recipe: run a model locally

1. `hardware` + `recommend_local_models`. Tested setups, best first:
   - Apple silicon, 112 GB+: **Qwen3.8-Flash-Next 4.7 bpw** on mlx-serve,
     `ddalcu/Qwen3.8-Flash-Next-MLX-Serve-iQ-MLX-4.7bpw`, port 11234, context 262144, extra args
     `--max-tokens 32768 --os-reserve-gib 4`; ~107 GB download, ~75 GB in memory, ~95–160 tok/s. 60/60 in
     KannaCode's benchmark, the best of every model tested, cloud ones included.
   - Apple silicon, 48 GB+: **Qwen3.8-27B 8-bit**, `ddalcu/Qwen3.8-27B-MLX-Serve-8bit`, port 11235, context 262144,
     `--max-tokens 32768`; ~33 GB. 55/60.
   - Apple silicon, 32–48 GB: `ddalcu/Qwen3.8-27B-MLX-Serve-4bit`, context 131072.
   - Linux + NVIDIA 40 GB+: vLLM `Qwen/Qwen3.8-27B-FP8`. NVIDIA 20 GB+: Ollama `qwen3.8:27b-q4`.
2. `list_server_types`; if not installed, `install_server_software(kind)` (visible terminal).
3. `add_server(kind, model, label, port, context, extra_args, alias)`. If you already downloaded the model,
   point `model` at that folder (no second download).
4. `start_server(id)`. A first start that downloads takes a long time: follow `server_log`.
5. `use_server_with_kannacode(id, alias, make_default=true)`, then `test_model(alias)`.

mlx-serve flags: `--ctx-size` (context window), `--max-tokens` (reply cap for clients that don't send one; its
built-in default is only 100, KannaCode always sends its own), `--timeout 0` (no request timeout; Hinoki adds it),
`--os-reserve-gib` (RAM kept for macOS; 4 lets a 128 GB Mac use the full 262K context). mlx-serve aliases need
`effort_param: reasoning_effort` or the model runs with thinking OFF (use_server_with_kannacode sets it).

## Cloud and API models

- Anthropic: `add_model(alias="claude", provider="anthropic", model_id="claude-sonnet-5-5", context=1000000)`, then
  `set_api_key("claude", key=...)` or `env_var="ANTHROPIC_API_KEY"`. Other Claude models: claude-opus-5-5
  (strongest), claude-haiku-4-5-20251001 (fastest).
- Ollama Cloud: `add_model(alias, provider="ollama", host="http://127.0.0.1:11434", model_id="<tag>:cloud",
  effort_param="think", tool_style="linked")`. Needs the Ollama app running and signed in (`ollama signin`) and a
  recent Ollama (old versions drop some models' tool calls). `linked` pairs tool calls with their results, which
  some cloud models need to avoid stalling.
- OpenAI, Gemini, Mistral: `provider` openai / gemini / mistral, a model id, and a key.
- Any OpenAI-compatible endpoint: `provider="openai", base_url="https://…/v1"`.
- Then `set_default_model` if wanted, and `test_model`. 

## MCP servers

KannaCode can use any MCP server. `add_mcp_server(name, command=[argv…], env, scope)`. Global scope is
`~/.kanna/mcp.json` (starts without consent); project scope is the project's `.kanna/mcp.json` (needs `/mcp allow
<name>` once, since it travels with the repo). Mark read-only tools with `read_only` so they skip approval. New or
changed servers load at the next KannaCode session; `/mcp` inside KannaCode shows their live state. Example, a
GitHub server: `["npx", "-y", "@modelcontextprotocol/server-github"]` with
`env={"GITHUB_PERSONAL_ACCESS_TOKEN": {"env": "GITHUB_TOKEN"}}`.

## SSH: remote development and remote GPUs

Two different things; they're set up differently:
- **Remote development** (code, terminals and language servers on the other machine; Hinoki is the client):
  `connect_ssh(host, folder)`. The window reloads into the remote (the first connect installs Hinoki's backend
  there). `disconnect_ssh` returns. `hinoki.workspaceMode`: local, remote (always open `hinoki.remote.host` /
  `hinoki.remote.folder` at launch) or ask.
- **A model on a remote GPU box, used from here:** `add_server(..., ssh_host=host)` then `start_server`; Hinoki
  starts it there under nohup and tunnels the port to 127.0.0.1 here, so `use_server_with_kannacode` works as if
  local. `check_ssh_host` shows the GPUs and which servers are installed there; `install_server_software(kind,
  ssh_host)` installs remotely.

New host: `add_ssh_host(alias, hostname, user, port, identity_file)`, then `check_ssh_host(alias)`. Hinoki needs
key-based login; if the check fails on authentication, `open_terminal("ssh-copy-id <alias>")` to enter your
password once (with no key yet, `ssh-keygen -t ed25519` first).

## Benchmarks

The Benchmarks view is the dashboard of KannaCode's model benchmark: status and live run (tok/s in and out,
tool calls, current task), a models table (pass rate, timeouts, average minutes, calls, input/cached/output
tokens, tok/s, estimated cost), and a task × model grid. "Run benchmark…" starts one. Results come from the
KannaCode checkout's `benchmarks/models/results/*.jsonl` (`hinoki.bench.repository`; extra files
`hinoki.bench.extraResultFiles`; harness work dir `hinoki.bench.workDir`; prices for cost estimates
`hinoki.bench.prices`). The bar-chart icon on the left is the compact live view.

## Updating Hinoki

Hinoki updates itself from github.com/DarrellBest/hinoki-releases: at startup (at most daily) it reads the newest
release's `latest.json` and offers Update / Release Notes / Skip This Version / Later. Update downloads the build
for this machine, verifies its sha256, saves open files, restarts and swaps in the new app (macOS app, Linux
AppImage) or opens the system installer (deb/rpm). Check now: **Hinoki: Check for Updates** in the command palette.

## Hinoki settings

| Key | Default | Meaning |
|---|---|---|
| `hinoki.kannacode.model` | "" | alias Hinoki launches KannaCode with (empty: KannaCode's default) |
| `hinoki.kannacode.mode` | "" | approval mode passed as --mode |
| `hinoki.kannacode.extraArgs` | [] | extra kannacode flags |
| `hinoki.kannacode.persistentSession` | auto | tmux-backed sessions (auto, always, never) |
| `hinoki.kannacode.webSearch` | on | on, ask, docs, off |
| `hinoki.kannacode.path` | "" | kannacode executable override |
| `hinoki.kannacode.updateUrl` | kannacode-releases latest.json | update channel |
| `hinoki.kannacode.checkForUpdates` | true | daily update check |
| `hinoki.kannacode.repository` | "" | git URL or checkout for Install/Update from source |
| `hinoki.workspaceMode` | local | local, remote, ask |
| `hinoki.remote.host` / `hinoki.remote.folder` | "" | remote-first target |
| `hinoki.bench.*` | | benchmark data sources and prices (see Benchmarks) |
| `hinoki.setup.completed` | false | the setup wizard has been finished |
| `hinoki.updates.checkAutomatically` | true | check for a new Hinoki at startup (daily) |
| `hinoki.updates.url` | hinoki-releases latest.json | Hinoki's update channel |

Everything else (editor, Python, git, terminal, theme, AI features…): `list_settings(filter)` gives keys, values
and descriptions; `set_setting(key, value, scope)` changes them (user or workspace scope).

## Troubleshooting

| Symptom | Cause and fix |
|---|---|
| KannaCode: "can't use http://127.0.0.1:PORT … Connection refused" | The model's server is not running. `list_servers` → `start_server`. If it is not in Models & Servers, `add_server` pointing at the existing model. |
| Server log `InsufficientMemory` / the model won't load | Not enough free RAM (~77 GB for Qwen Flash). Close browser tabs or other big apps, or run `sudo purge` in Terminal.app, then start again. One big local model at a time. |
| Local Qwen answers without thinking / weak answers | The alias lacks `effort_param: reasoning_effort` (mlx-serve runs thinking off without it). Run `use_server_with_kannacode` again. |
| "reasoningContent is not supported in multi-turn conversations…" warning | Harmless: earlier turns' thinking is not resent to OpenAI-style servers, and Qwen's template discards it anyway. |
| A cloud model stops after tool calls / empty replies | Use `tool_style="linked"` on the alias; update Ollama. |
| A KannaCode session can't see new MCP tools | MCP servers load at session start: restart the session. |
| Disk filling during long runs | A recursive `grep -r … .` from the project root reads KannaCode's own `.kanna/tool-output/` captures; exclude `.kanna`. |
| SSH host check fails on auth | No key installed: `ssh-copy-id` in a terminal. |
| Setup wizard keeps opening | `hinoki.setup.completed` is false; finish it or set it true. |
| The Mac slept and runs died | Servers are caffeinated, but closing a MacBook lid sleeps it anyway unless in clamshell mode or `sudo pmset -a disablesleep 1`. |

