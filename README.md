# claude-code
Tips and tricks I use to optimize my experience with Claude Code.

## Keyboard shortcuts
- **Shift + Tab**: Toggles modes between `Normal`, `Auto-accept` and `Plan` mode. Always consider starting with plan mode to validate the work.
- **Esc / Ctrl + C**: Interrupts. Use this when you see that the thinking goes for too long or goes off track.
- **Esc + Esc**: Rewinds to a previous prompt to restore a context point.
- **Cmd + Shift + 4**: Screenshot something (on Mac) then drag and drop it into Claude.
- **Ctrl + D**: Closes the Claude Code session.
- **Ctrl + R**: Search command in history.
- **Ctrl + _**: Undo last action.

More: https://code.claude.com/docs/en/keybindings 

## Essential commands
- **/clear**: Clears the entire context, use this often when asking a new work/feature.
- **/context**: Visual view to audit the current context used, if too many things are irrelevant, clear or disable things.
- **/compact**: Not really recommended since there is auto-compacting but can be helpful if you want to send the compacted context to another instance to prompt it later on.
- **/model**: Use the best cost effective model for the work to be done. Opus (expensive) is good for thinking/planing, Sonnet (normal cost), is good for executing defined tasks and Haiku (cheap) is good for quick answers.
- **/resume**: Recovers a session that was killed to reuse a context.
- **/mcp**: Shows the MCP Servers that are installed, be cautious because it can blow your context window.
- **/agents**: Manage or create an agent.
- **/rewind**: Restores code or conversations to a given point.
- **/init**: Creates a CLAUDE.md file.
- **/permissions**: An allowlist, ask and denylist of commands to run.
- **/cost**: Shows token usage and cost for the current session.
- **#**: Adding # followed by a file path pulls that file into context directly.
- **@**: References a file inline in your prompt.
- **/help**: Lists all other available commands.

## Global (`~/.claude/CLAUDE.md`)

Here are few sections I add under my global Claude configuration.
- Cost Optimization
- Guidelines

```MD
## Cost Optimization
Follow these tips for efficient token usage without sacrificing quality:

- **Forbidden Directories**: Never read or explore these: `node_modules/, .git/, dist/, build/, .next/, coverage/, vendor/, __pycache__/, .venv/, .tox/, .env, package-lock.json, yarn.lock, pnpm-lock.yaml`.
- **Response Style**: Be direct and concise. Skip preamble like "Great question!" or "Sure, I can help with that". When fixing bugs, state the root cause briefly, then show the fix. Don't repeat my instructions back to me. For simple tasks, keep responses short. Match response length to task complexity. For small changes, show only the relevant diff, not the entire file. Use file:line format for precise references.
- **Smart File Reading**: Use `grep` and `find` to locate things before reading entire files. When searching for a function, pattern, or reference: prefer `grep -rn "pattern" src/` or `find . -name "*.ts" -path "*/routes/*"` over reading files one by one. Read only the files needed for the current task. You can explore when needed, just be targeted about it. When I provide `@file` references, start there before looking elsewhere. 
- **Model Selection**: Sonnet: default for ~80% of tasks. Haiku: renames, boilerplate, formatting, simple lookups. Opus: architecture decisions, complex debugging, nuanced code review. If Ollama is available, prefer it for easy/trivial tasks.
- **Session Habits**: Between each unrelated tasks, run the `/clear` command to stop paying for stale history. `/compact` every ~30 min, don't wait for auto-compact. Batch related edits into single prompts. Use plan mode (Shift+Tab ×2) before expensive multi-step operations. Use Explore subagents for codebase searches to keep main context clean. Disable unused MCP servers (/mcp), each costs ~5-15K tokens of context.
- **Compaction**: When using /compact, preserve: changes made, current task state, errors, decisions. Discard raw file contents (re-readable), verbose logs, dead-end explorations.
- **Search Efficiently**: `grep -rn "pattern" src/` to find references. `find . -name "*.ts" -not -path "*/node_modules/*"` to find files. `head -30 file.ts` to peek at file shape. `git diff --name-only` to see what changed.
- **Edits**: Show only changed code with enough context to locate the edit, not entire files. Batch related changes into one response. Don't touch unrelated code.

## Guidelines
Follow these guidelines for all changes unless explicitly told otherwise:

- **Project Context**: Before making changes, read the README and any existing documentation to understand what the project does, its architecture, and key design decisions. If unclear, ask before assuming.
- **Dependencies**: Always prefer standard libraries. Before suggesting any third-party dependency, verify online that it is actively maintained (updated within the last 4 months), not archived, and has no known security vulnerabilities. When a third-party package is necessary, prefer well-established, widely-adopted options.
- **File Structure**: Before creating new files or directories, review the existing project structure and follow its conventions. If the project is new or lacks clear structure, look up the official documentation and widely-adopted community conventions for the language/framework in use, and follow those. Keep the structure flat and simple — avoid deep nesting. Group files by feature or responsibility, not by type, unless the framework convention says otherwise.
- **Code Style**: Follow the existing code style in the project. If no style is established, look up the official style guide for the language/framework in use and follow it. Always respect any configured linters or formatters (e.g., Prettier, ESLint, Black) and never override their rules.
- **Testing**: Before writing tests, check for an existing test setup and follow its patterns, naming conventions, and directory structure. If no tests exist, look up the recommended testing framework and conventions for the language/framework in use. Always run tests after making changes to verify nothing is broken. Include passing and failing test cases when writing new tests.
- **Error Handling**: Never silently swallow errors. Follow the language/framework's idiomatic error handling patterns. Always provide meaningful error messages that help with debugging. Don't use generic catch-all handlers unless re-throwing or logging with context.
- **Logging**: Add meaningful log output for important operations, errors, and state changes. Use appropriate log levels (debug, info, warn, error). Never log sensitive data like tokens, passwords, or personal information.
- **Security**: Never hardcode secrets, API keys, or credentials. Use environment variables or a secrets manager. Sanitize all user inputs. Follow OWASP best practices for the language/framework in use when handling authentication, authorization, or data exposure.
- **Documentation**: Add concise inline comments only when the "why" isn't obvious from the code. Keep README and any existing docs up to date when making changes that affect setup, usage, or architecture. Don't over-comment obvious code.
- **Performance**: Don't prematurely optimize, but avoid known anti-patterns (e.g., N+1 queries, unnecessary re-renders, blocking the main thread). If a change could impact performance at scale, flag it.
- **Validation**: After making code changes, always run the project's build and lint commands to verify the project compiles and passes all checks before considering the task complete. If any errors arise, fix them before moving on.
- **Common Commands**: Look for a Makefile, package.json scripts, or equivalent before inventing commands. Use the project's established build, lint, test, and deploy commands. Document any new commands you introduce.
- **Do's and Don'ts**: Never modify generated files, lock files, or configuration files without being asked. Don't change code unrelated to the current task. Don't introduce patterns or paradigms that are inconsistent with the rest of the codebase.
- **Git Conventions**: Before committing, check for existing commit message conventions in the project history and follow them. If none exist, use clear, concise commit messages in the imperative mood (e.g., "add: user authentication"). Keep commits focused on a single change. Never run push, force push, rebase, or branch deletion commands without being asked. Be sure to work on a clean branch before doing any risky changes.
- **Prefer editing over rewriting**: When modifying existing files, use targeted edits rather than rewriting the whole file if possible. Edit the entire file only if you need to refactor to make things cleaner.
- **Ask before deleting**: Never delete files, functions, or significant blocks of code without confirmation. Only do it if they are no longer needed or used.
- **Language/framework version awareness**: Check the project's runtime version (e.g., Node version in .nvmrc, Python in pyproject.toml) before suggesting syntax or APIs that may not be supported.
```

## Project level (`.claude/CLAUDE.md`)
WIP