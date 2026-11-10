# Running with a dedicated coding agent for intermediate users

This guide walks intermediate developers through connecting a coding agent harness to a local Ollama server, choosing and configuring a model for agent workloads, and initializing Git in your project. It assumes Ollama is installed and working on your machine. It is written to apply to any coding agent or harness that supports OpenAI-compatible or Ollama-native providers, and to any Ollama model that supports the features your harness needs.

## Prerequisites

- Ollama installed and running (`ollama --version` returns a version number)
- At least one model pulled, such as `ollama pull <model-name>`
- Git installed (`git --version`)
- A project directory you want the agent to work in

Confirm the server is reachable before you start:

```bash
curl http://localhost:11434/api/tags
```

If this returns a JSON list of models, the server is ready.

## Choosing a Harness

A harness is the application that sits between you, the model, and your files. It sends prompts, executes tool calls such as reading files and running shell commands, and applies edits. Pick one based on where you work:

| Harness type | Examples | Best for | Tradeoffs |
|---|---|---|---|
| IDE extension | Cline, Continue, Roo Code | Working inside VS Code or JetBrains with visual diffs | Settings live in the UI, so changes are harder to script |
| Terminal agent | Claude Code, Codex, OpenCode, Hermes Agent, other CLI agents | Scripting, remote sessions, keyboard-driven workflows | Less visual feedback; review diffs with `git diff` |
| Desktop app | Various Ollama-compatible GUIs | Non-terminal workflows | Fewer controls for tool permissions and context |

Whichever you choose, read its documentation for three things: how it connects to a provider, whether it requires tool calling from the model, and how it asks for permission before running commands.

## Choosing a Model

Not every model works well as a coding agent. Agent harnesses depend on three capabilities:

- **Tool calling:** The model must return structured tool requests that the harness can execute. Without this, the agent can only chat.
- **Context length:** The model must handle the prompt size your harness sends, including file contents and tool definitions.
- **Coding ability:** Larger models generally write and edit code more reliably, but they need more memory and run more slowly.

Check a model's capabilities before you commit to it. There are multiple ways you can do this... *You can pull a model and test it yourself but this can be time consuming!*

If you want to get a better idea of how well a model will perform, look at sites like [Artificial Analysis](https://artificialanalysis.ai/leaderboards/models), [LLM-Stats](https://llm-stats.com/), and [BenchLM](https://benchlm.ai/). These sites contain benchmark data and leaderboards for model intelligence. If you want to quickly look at how "good" a model might be, look at the leaderboards and see what models might work for you. Since you are running locally, you can filter for different model types. Check models that are between 4-20 billion parameters in size as that is what usually fits on most people's hardware.

**Please know that benchmark scores are not a perfect reflection of how "smart" a model is! They are average scores across hundreds or thousands of different tests. You can look at specific benchmark scores (like TerminalBench) to get an idea for how it might perform for your specific task.** 

Look for tool or function calling support in the output, and note the context length. Ollama's model library also lists capabilities for each model. Model support changes over time, so verify with the current listing rather than relying on past recommendations.

Match the model size to your hardware. As a rough guide, models in the 7B to 9B range fit in about 12-16 GB of RAM, while larger models need substantially more memory or a GPU with enough VRAM. **Quantized** variants reduce memory use at some cost in quality. 

**It is strongly recommended that you use a quantized model. Generally the "best" quantization for most people is Q4.**

**"Quantization" is basically just compressing the model, a lot like a .zip folder. Ollama provides quantized models by default!**

## Configuring the Provider

Most harnesses let you pick a provider. For Ollama, you will typically use one of two connection styles:

| Setting | Ollama native | OpenAI-compatible |
|---|---|---|
| Base URL | `http://localhost:11434` | `http://localhost:11434/v1` |
| API key | Not required | Any non-empty string, such as `ollama` |
| Model name | Exactly as shown in `ollama list` | Exactly as shown in `ollama list` |

Use the native option if your harness has an Ollama provider. Many harnesses support being launched by Ollama. For example, to use Claude Code with Ollama, just run `ollama launch claude`. Use the OpenAI-compatible option if the harness only accepts a generic OpenAI endpoint. Check the harness documentation for the exact field names, because they vary.

### Set the Context Window

Agent prompts often include file contents, tool definitions, and conversation history, which can exceed Ollama's default context length. When this happens, the model silently drops earlier content, and the agent appears to forget instructions. Set the context window explicitly in one of two ways:

**Option A: Set a server-wide default.** Set the `OLLAMA_CONTEXT_LENGTH` environment variable before starting Ollama. This applies to all models, so it may use more memory than you need for casual chats. **If you have the Ollama desktop app you can simply go to the app's settings and toggle the context length there as well.**


**Option B: Create a custom model with a Modelfile.** This persists across restarts and is easy to share.

```text
FROM <model-name>
PARAMETER num_ctx 32768
```

Save this as `Modelfile`, then build it with a name you choose:

```bash
ollama create my-agent-model -f Modelfile
```

Use `my-agent-model` as the model name in your harness.


Larger context windows use more RAM and VRAM. If responses slow to a crawl or the machine runs out of memory, reduce `num_ctx` to 16384 or 8192.

### Verify the Connection

Send a short test prompt from your harness, such as "List the files in this directory." If the agent returns an error about the model not found, check the model name against `ollama list`. If it returns an error about the connection, confirm the base URL and that Ollama is running. If the model prints tool calls as plain text instead of executing them, the model may lack tool calling support, the harness may expect a different tool format, or your Ollama version may have a compatibility issue. Try a different model or version to narrow it down.

## Setting Permissions

Coding agents can modify files and run commands, so set permission rules before your first real task:

- Require approval for file writes and shell commands until you trust the workflow.
- Keep the agent inside the project directory.
- Never give it access to credentials, SSH keys, or `.env` files. Exclude them in your harness settings and `.gitignore`.
- Review every diff before accepting it.

## Initializing Git

Git gives you a checkpoint before the agent makes changes, so you can undo them easily. **You can have your agent do this**, but if you want to do it yourself, here's how:

Initialize it in your project root:

```bash
cd path/to/your/project
git init
git config user.name "Your Name"
git config user.email "you@example.com"
```

Create a `.gitignore` file to keep generated and sensitive files out of version control:

```gitignore
node_modules/
.env
.env.*
*.log
dist/
build/
.DS_Store
Modelfile
```

Then make your baseline commit:

```bash
git status
git add .
git commit -m "Initial commit"
git log --oneline
```

Before each agent session, commit or stash your current work. After the session, run `git diff` to review changes, and use `git restore <file>` or `git reset --hard HEAD` only when you intend to discard them.

## Recommended Workflow

1. Commit your current state.
2. Start a fresh chat or session in the harness so old context does not leak into the new task.
3. Give the agent one scoped task at a time.
4. Review the diff and run your tests.
5. Commit the accepted changes with a descriptive message.

## Troubleshooting

| Symptom | Likely cause | Fix |
|---|---|---|
| Connection refused | Ollama is not running | Start Ollama or run `ollama serve` |
| Model not found | Name mismatch | Match the name exactly to `ollama list` |
| Agent forgets early instructions | Context window too small | Increase `num_ctx` in your Modelfile |
| Very slow responses | Model too large for available memory | Use a smaller model or reduce `num_ctx` |
| Tool calls printed as text | Model lacks tool calling, harness format mismatch, or Ollama version issue | Check `ollama show`, try another model, or test a different Ollama version |
| Git refuses to commit | Identity not set | Run `git config user.name` and `git config user.email` |
