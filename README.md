# 🤖 AI Agent

A powerful, extensible AI agent framework that executes tasks using tools, manages multi-turn conversations, and supports complex workflows — all from your terminal.

---

## Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Installation](#installation)
- [Quick Start](#quick-start)
- [Usage](#usage)
  - [Interactive Mode](#interactive-mode)
  - [Single-Run Mode](#single-run-mode)
  - [Streaming Responses](#streaming-responses)
- [Built-in Tools](#built-in-tools)
- [Configuration](#configuration)
  - [Model Settings](#model-settings)
  - [Tool Allowlisting](#tool-allowlisting)
  - [Shell Environment Policies](#shell-environment-policies)
  - [MCP Server Configuration](#mcp-server-configuration)
- [Safety & Approval Policies](#safety--approval-policies)
- [Context Management](#context-management)
- [Session Management](#session-management)
- [MCP Integration](#mcp-integration)
- [Subagents](#subagents)
- [Loop Detection](#loop-detection)
- [Hooks System](#hooks-system)
- [Terminal UI & Commands](#terminal-ui--commands)
- [Architecture](#architecture)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

AI Agent is a fully-featured, terminal-based AI agent that combines language model intelligence with a rich set of tools for real-world task execution. It supports file operations, web access, shell execution, memory, and more — all within a safe, configurable environment with user-controlled approval policies.

Whether you're automating developer workflows, investigating codebases, or building custom agent pipelines, AI Agent provides the scaffolding to get there fast.

---

## Features

### Core Functionality
- **Interactive & single-run modes** — Use conversationally or pipe in one-shot tasks
- **Streaming text responses** — See output as it's generated in real time
- **Multi-turn conversations with tool calling** — Full context retained across turns
- **Configurable model settings** — Choose your model, set temperature, and tune behavior

### Built-in Tools
| Category | Tools |
|---|---|
| File Operations | Read, write, edit files |
| Directory Operations | List directories, glob pattern search |
| Text Search | Grep for pattern matching across files |
| Shell Execution | Run arbitrary shell commands |
| Web Access | Search the web and fetch URLs |
| Memory | Store and retrieve persistent information |
| Todo | Manage and track task lists |

### Safety & Approval
- Multiple approval policies: `on-request`, `auto`, `never`, `yolo`
- Dangerous command detection and blocking
- Path-based safety checks
- User confirmation prompts for all mutating operations

### Context Management
- Automatic context compression when approaching token limits
- Tool output pruning to keep context lean
- Real-time token usage tracking

### Session Management
- Save and resume sessions across runs
- Create named checkpoints at any point
- Persistent session storage

### MCP Integration
- Connect to any Model Context Protocol (MCP) server
- Use external tools from MCP servers seamlessly
- Supports both `stdio` and `HTTP/SSE` transports

### Subagents
- Spawn specialized subagents for discrete tasks
- Built-in subagents: **Codebase Investigator**, **Code Reviewer**
- Define custom subagents with their own tools, instructions, and limits

### Loop Detection
- Detects repeating action patterns automatically
- Prevents infinite loops during agent execution

### Hooks System
- Execute scripts before/after agent runs
- Execute scripts before/after individual tool calls
- Error-handling hooks for custom recovery logic
- Support for custom commands and scripts

---

## Installation

### Prerequisites
- Node.js >= 18.x
- npm or yarn
- (Optional) API key for your chosen language model provider

### Install via npm
```bash
npm install -g ai-agent
```

### Install from source
```bash
git clone https://github.com/your-org/ai-agent.git
cd ai-agent
npm install
npm run build
npm link
```

### Set up your API key
```bash
export ANTHROPIC_API_KEY=your_api_key_here
# or add to your ~/.bashrc / ~/.zshrc
```

---

## Quick Start

```bash
# Start an interactive session
ai-agent

# Run a single task
ai-agent "List all TypeScript files in the src directory"

# Run with a specific model
ai-agent --model claude-sonnet-4-20250514 "Summarize the README"

# Run with auto-approval (no confirmation prompts)
ai-agent --approval auto "Refactor the auth module"
```

---

## Usage

### Interactive Mode

Launch a persistent conversational session:

```bash
ai-agent
```

The agent maintains full conversation history. You can ask follow-up questions, reference prior tool results, and issue multi-step instructions naturally.

```
> Find all TODO comments in the codebase
> Now create a GitHub issue summary for each one
> Save the summary to todo-report.md
```

### Single-Run Mode

Pass a task directly as an argument for non-interactive, one-shot execution:

```bash
ai-agent "Generate a test suite for src/auth.ts"
```

Combine with shell pipes for scripting:

```bash
echo "Explain this code" | cat - src/utils.ts | ai-agent
```

### Streaming Responses

Streaming is enabled by default. Disable it with:

```bash
ai-agent --no-stream "Summarize this project"
```

---

## Built-in Tools

### File Tools

```
read_file(path)              — Read the contents of a file
write_file(path, content)    — Write or overwrite a file
edit_file(path, edits)       — Apply targeted edits to a file
```

### Directory Tools

```
list_directory(path)         — List files and subdirectories
glob_search(pattern)         — Search using glob patterns (e.g., **/*.ts)
```

### Search Tools

```
grep(pattern, path)          — Search for a regex pattern across files
```

### Shell Tools

```
shell(command)               — Execute a shell command
```

> ⚠️ Shell commands are subject to the configured approval policy and dangerous command detection.

### Web Tools

```
web_search(query)            — Search the web
web_fetch(url)               — Fetch and read a web page
```

### Memory Tools

```
memory_store(key, value)     — Persist a piece of information
memory_retrieve(key)         — Retrieve stored information
```

### Todo Tools

```
todo_add(task)               — Add a task to the list
todo_list()                  — View all tasks
todo_complete(id)            — Mark a task as done
```

---

## Configuration

Configuration can be provided via a config file (`agent.config.json`) or CLI flags.

### Model Settings

```json
{
  "model": "claude-sonnet-4-20250514",
  "temperature": 0.7,
  "maxTokens": 8192
}
```

| Option | Type | Default | Description |
|---|---|---|---|
| `model` | string | `claude-sonnet-4-20250514` | Model to use |
| `temperature` | float | `0.7` | Sampling temperature (0.0 – 1.0) |
| `maxTokens` | integer | `8192` | Maximum tokens per response |

### Tool Allowlisting

Restrict which tools the agent is permitted to use:

```json
{
  "allowedTools": ["read_file", "list_directory", "grep", "web_search"]
}
```

### Shell Environment Policies

Control how the agent interacts with the shell environment:

```json
{
  "shell": {
    "allowedCommands": ["git", "npm", "ls", "cat"],
    "blockedCommands": ["rm -rf", "sudo", "curl | bash"],
    "workingDirectory": "./project"
  }
}
```

### MCP Server Configuration

```json
{
  "mcpServers": [
    {
      "name": "my-mcp-server",
      "transport": "stdio",
      "command": "node",
      "args": ["./mcp-server.js"]
    },
    {
      "name": "remote-server",
      "transport": "http",
      "url": "https://my-mcp-server.example.com/sse"
    }
  ]
}
```

---

## Safety & Approval Policies

The agent supports four approval policies to control when it asks for user confirmation before taking actions:

| Policy | Behavior |
|---|---|
| `on-request` | Asks for approval only when the agent explicitly requests it (default) |
| `auto` | Automatically approves safe operations; prompts for dangerous ones |
| `never` | Always prompts before any mutating or shell operation |
| `yolo` | No prompts — all operations proceed immediately ⚠️ |

Set via CLI:

```bash
ai-agent --approval never "Clean up temp files"
```

Or in config:

```json
{
  "approvalPolicy": "auto"
}
```

### Dangerous Command Detection

The agent automatically flags patterns like:
- `rm -rf` on broad paths
- Writing to system directories
- Commands with `sudo`
- Piping untrusted content to shells

Flagged commands are always surfaced for explicit user confirmation, regardless of the approval policy.

---

## Context Management

The agent monitors token usage continuously and applies the following strategies when the context window approaches its limit:

1. **Tool output pruning** — Trims verbose tool results while preserving key data
2. **Context compression** — Summarizes older conversation turns to free up space
3. **Token usage display** — Shows live token counts in the terminal UI

These strategies ensure long-running sessions remain stable and coherent.

---

## Session Management

### Save a Session

```bash
/save my-session
```

### Resume a Session

```bash
ai-agent --resume my-session
# or from within the agent:
/resume my-session
```

### Checkpoints

Create a named checkpoint at any point in a session:

```bash
/checkpoint before-refactor
```

Restore to a prior checkpoint:

```bash
/restore before-refactor
```

Sessions are stored persistently in `~/.ai-agent/sessions/`.

---

## MCP Integration

AI Agent supports the [Model Context Protocol (MCP)](https://modelcontextprotocol.io), allowing you to connect external tool servers and expand the agent's capabilities.

### Supported Transports
- **stdio** — Local process communication
- **HTTP/SSE** — Remote server communication

### Using MCP Tools

Once configured, MCP tools appear alongside built-in tools and are called transparently by the agent. Use `/mcp` in the terminal UI to view connected servers and their available tools.

---

## Subagents

Subagents are specialized agent instances that can be spawned to handle focused subtasks.

### Built-in Subagents

| Name | Description |
|---|---|
| `codebase-investigator` | Explores and summarizes codebases, file structures, and dependencies |
| `code-reviewer` | Reviews code for bugs, style issues, and improvement opportunities |

### Custom Subagents

Define your own subagents in the config:

```json
{
  "subagents": [
    {
      "name": "test-writer",
      "description": "Writes unit tests for given functions",
      "tools": ["read_file", "write_file", "shell"],
      "instructions": "You are an expert at writing clean, thorough unit tests.",
      "maxTurns": 10
    }
  ]
}
```

Invoke a subagent:

```bash
ai-agent --subagent test-writer "Write tests for src/parser.ts"
```

---

## Loop Detection

The agent monitors its own action history and detects when it is repeating the same sequence of tool calls. When a loop is detected:

1. Execution is paused
2. The user is notified with a summary of the repeated actions
3. The agent prompts for a new instruction or intervention

This prevents runaway loops from wasting tokens or causing unintended side effects.

---

## Hooks System

Hooks let you run custom scripts at key points in the agent lifecycle.

### Available Hook Points

| Hook | Trigger |
|---|---|
| `before-run` | Before the agent begins a task |
| `after-run` | After the agent completes a task |
| `before-tool` | Before any tool is called |
| `after-tool` | After any tool completes |
| `on-error` | When an error occurs |

### Hook Configuration

```json
{
  "hooks": {
    "before-run": "./scripts/setup.sh",
    "after-run": "./scripts/notify.sh",
    "before-tool": "./scripts/log-tool.sh",
    "on-error": "./scripts/alert.sh"
  }
}
```

Hook scripts receive context as environment variables (e.g., `AGENT_TASK`, `TOOL_NAME`, `TOOL_RESULT`).

---

## Terminal UI & Commands

The terminal UI provides real-time visualization of tool calls, streaming output, and session state.

### Available Commands

| Command | Description |
|---|---|
| `/help` | Show all available commands |
| `/config` | View or edit the current configuration |
| `/tools` | List all available tools |
| `/mcp` | Show connected MCP servers and their tools |
| `/stats` | Display token usage and session statistics |
| `/save <name>` | Save the current session |
| `/resume <name>` | Resume a saved session |
| `/checkpoint <name>` | Create a named checkpoint |
| `/restore <name>` | Restore to a checkpoint |

---

## Architecture

```
ai-agent/
├── src/
│   ├── agent/           # Core agent loop and turn management
│   ├── tools/           # Built-in tool implementations
│   ├── subagents/       # Subagent definitions and runner
│   ├── mcp/             # MCP client and transport adapters
│   ├── hooks/           # Hooks system
│   ├── session/         # Session save/resume/checkpoint logic
│   ├── context/         # Context compression and token tracking
│   ├── safety/          # Approval policies and dangerous command detection
│   ├── ui/              # Terminal UI rendering
│   └── config/          # Configuration loading and validation
├── agent.config.json    # Default configuration file
├── package.json
└── README.md
```

---

## Contributing

Contributions are welcome! Please follow these steps:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit your changes: `git commit -m "feat: add my feature"`
4. Push to your branch: `git push origin feature/my-feature`
5. Open a Pull Request

Please read [CONTRIBUTING.md](./CONTRIBUTING.md) for code style guidelines, testing requirements, and the pull request process.

---

## License

This project is licensed under the [MIT License](./LICENSE).

---

> Built with ❤️ for developers who want AI that actually gets things done.
