# Hinoki

**Hinoki is a desktop IDE built around KannaCode, an AI coding agent.** It runs on macOS, Linux and Windows, and it
can run AI models on your own machine, on a GPU server you reach over SSH, or through a cloud provider.

This repository is where Hinoki is released: download it from [Releases](../../releases/latest), and read the
documentation here.

| | |
|---|---|
| **Download** | [Latest release](../../releases/latest) |
| **User guide** | [docs/GUIDE.md](docs/GUIDE.md) |
| **Updates and `latest.json`** | [docs/UPDATES.md](docs/UPDATES.md) |
| **What changed** | [CHANGELOG.md](CHANGELOG.md) |

## What you get

- **Projects, PyCharm-style:** a Projects home screen with your recent projects, and a New Project wizard with
  templates:
  - **Python:** an app with its own virtual environment via uv; a package with a src layout and pytest.
  - **Web and data:** FastAPI; data science with Jupyter.
  - **Other:** Node.js + TypeScript; an empty project.
  - **"Describe it to KannaCode":** KannaCode builds the project for you.

  You can also clone a repository or work on another machine.
- **A full IDE:** editor and language servers, debugger, git, terminals, test explorer, Jupyter notebooks and
  containers. It's built on Eclipse Theia and runs VS Code extensions from [Open VSX](https://open-vsx.org).
- **KannaCode built in:** Hinoki carries its own copy and installs it privately on first run, without touching your
  PATH or system Python. KannaCode drives the chat panel, inline AI and code completion.
- **KannaCode can operate the IDE.** Ask it to create a project, set up and start a model server, add a cloud model,
  connect to an SSH machine, install an extension, or explain any part of Hinoki, and it does the work. Anything
  that changes something asks for your approval first.
- **Models your way:**
  - **Local:** mlx-serve, mlx-vlm and mlx-lm on Apple silicon; vLLM, llama.cpp and Ollama.
  - **Remote GPU:** model servers started on an SSH host and tunnelled back to your machine.
  - **Cloud:** Anthropic, OpenAI, Gemini, Mistral and Ollama Cloud, or any OpenAI-compatible endpoint.
- **Setup wizard:** detects your hardware, recommends a model that fits, downloads it, starts it and tests it.
- **Remote development:** open a window on an SSH machine. Your code and terminals stay there, and Hinoki is the
  client.
- **Benchmarks:** a dashboard of KannaCode's model benchmark, with pass rates, tokens, cost and live tok/s.
- **Automatic updates:** Hinoki checks this repository's `latest.json` and updates itself.

## Install

| Platform | File | Notes |
|---|---|---|
| macOS (Apple silicon) | `Hinoki-<version>-arm64.dmg` | Open the DMG and drag Hinoki to Applications. The builds aren't notarized yet, so the first time, right-click Hinoki and choose **Open**. |
| Linux x64 | `Hinoki-<version>-x86_64.AppImage`, `-amd64.deb`, `-x86_64.rpm` | AppImage: `chmod +x Hinoki-*.AppImage` and run it. It updates itself in place. deb/rpm: `sudo apt install ./Hinoki-*.deb` or `sudo dnf install ./Hinoki-*.rpm`. |
| Linux arm64 | `Hinoki-<version>-arm64.AppImage`, `-arm64.deb`, `-aarch64.rpm` | As above. |
| Windows x64 | `Hinoki-Setup-<version>-x64.exe` | Run the installer. It isn't code-signed yet, so SmartScreen may ask you to confirm (More info → Run anyway). |

Every file's SHA-256 checksum is listed in that release's `latest.json`.

## First run

1. Open Hinoki. The **setup wizard** installs KannaCode (about a minute) and walks you through choosing a model:
   - **On this machine:** recommends the best model your RAM and GPU can run, then installs the server, downloads
     the model, starts it and tests it.
   - **A cloud provider:** paste an API key.
   - **An SSH GPU server**, or **any OpenAI-compatible URL**.
2. On the **Projects** screen, click **New Project** and pick a template, or open a folder, or clone a repository.
   Or ask KannaCode to "create a FastAPI project called my-api".
3. Talk to KannaCode in the **Chat** panel on the right, or open it in a terminal from the KannaCode view on the
   left.

Tested local models, best first:

| Machine | Model | Memory |
|---|---|---|
| Apple silicon, 128 GB | Qwen3.8-Flash-Next 4.7-bit on mlx-serve | ~75 GB |
| Apple silicon, 48 GB+ | Qwen3.8-27B 8-bit on mlx-serve | ~33 GB |
| Apple silicon, 32–48 GB | Qwen3.8-27B 4-bit on mlx-serve | ~20 GB |
| Linux, NVIDIA 40 GB+ | Qwen3.8-27B FP8 on vLLM | ~34 GB |
| Any NVIDIA 20 GB+ | Qwen3.8-27B 4-bit on Ollama | ~20 GB |

## Updating

Hinoki checks for a new version at startup, at most once a day, and offers to install it.
- **macOS and AppImage:** it downloads the new version, verifies its checksum, then restarts into it.
- **deb and rpm:** it hands the package to your system installer.

You can also run **Hinoki: Check for Updates** from the command palette at any time.

You can turn off the startup check with `hinoki.updates.checkAutomatically`, or point Hinoki at a different
channel with `hinoki.updates.url`. See [docs/UPDATES.md](docs/UPDATES.md).

## Where Hinoki keeps things

| Path | What |
|---|---|
| `~/.hinoki/` | Hinoki's settings, its private KannaCode install, model server configs and logs, and update downloads |
| `~/.kanna/` | KannaCode's models, settings, API keys (`keys.json`, readable only by you), MCP servers and skills |

Uninstalling the app leaves these folders; delete them to remove everything.

## Reporting problems

Open an issue in this repository. Include your Hinoki version (Help → About), your OS, and what you saw.
