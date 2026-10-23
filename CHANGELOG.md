# Changelog

## 0.1.4

- **KannaCode chat approvals work:** the Allow and Deny buttons on "KannaCode wants to run…" prompts (and the
  buttons on KannaCode's questions) can be clicked again.
- **Long commands wrap** inside approval prompts instead of running off the right edge.
- **Launch KannaCode opens the chat panel.** Each model in the KannaCode view also has a button to chat with it
  and a button to open it in a terminal.

## 0.1.3, the first public release

- **Projects, PyCharm-style:**
  - **Projects home screen:** recent projects with search, git branch and project type.
  - **New Project wizard:** Python (uv + .venv), Python package, FastAPI, data science (Jupyter), Node.js +
    TypeScript, empty, or "describe it to KannaCode".
  - **File menu:** Clone Repository and Close Project.
  - **Project view:** the file tree is now called the Project view.
- **KannaCode operates Hinoki:**
  - **52 built-in tools** for projects, files, terminals, IDE commands, extensions, tasks, model servers, cloud
    models and keys, MCP servers, SSH remotes and settings.
  - **A complete Hinoki guide** that KannaCode reads, so it can answer questions and suggest next steps.
- **Web search** for KannaCode, on by default (`hinoki.kannacode.webSearch`: on, ask, docs or off).
- **Automatic updates:** Hinoki checks this repository's `latest.json`, verifies the download's checksum and
  updates itself.
- **Setup wizard fixes:**
  - It reuses a model server you already set up instead of downloading the model again.
  - Its model test works when Hinoki is opened from the Dock or Finder.
- **KannaCode's built-in defaults** are the commercial AWS Bedrock ones.
