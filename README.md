# claude-code
Tips and tricks I use to optimize my experience with Claude Code.

## Table of Contents
- [Keyboard shortcuts](#keyboard-shortcuts)
- [Essential commands](#essential-commands)
- [Global (`~/.claude/CLAUDE.md`)](#global-claudeclaudemd)
- [Project level (`.claude/CLAUDE.md`)](#project-level-claudeclaudemd)
- [Agents](#agents)
- [Skills](#skills)
- [Plugins](#plugins)
- [My way of working](#my-way-of-working)

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
The project-level `CLAUDE.md` is where you define context that's specific to this repository. Since your global config already covers universal guidelines, cost optimization, and code style defaults, the project file should focus on what makes this project unique. Avoid duplicating what's in `~/.claude/CLAUDE.md`.

Here's a template I use:

```markdown
# Project: <project-name>

## Overview
<!-- One paragraph max. What does this project do, who is it for, and what's the core architecture? -->

## Tech Stack
- **Language**: TypeScript 5.x (strict mode)
- **Framework**: Next.js 14 (App Router)
- **Database**: PostgreSQL via Prisma
- **Testing**: Vitest + Playwright
- **Package Manager**: pnpm

## Project Structure
<!-- Describe the key directories so Claude doesn't have to explore blindly -->

src/
  app/          # Next.js app router pages and layouts
  lib/          # Shared utilities, DB client, auth helpers
  components/   # React components, organized by feature
  services/     # Business logic, external API integrations
  types/        # Shared TypeScript types and interfaces
prisma/         # Schema and migrations
e2e/            # Playwright end-to-end tests

## Common Commands
pnpm dev          # Start dev server
pnpm build        # Production build
pnpm test         # Run unit tests
pnpm test:e2e     # Run Playwright tests
pnpm lint         # ESLint + Prettier check
pnpm db:migrate   # Run Prisma migrations
pnpm db:seed      # Seed the database

## Key Conventions
<!-- Things specific to THIS project that override or extend global defaults -->
- API routes return `{ data, error }` shape consistently.
- All DB queries go through service files, never called directly from routes or components.
- Feature flags are managed via `src/lib/flags.ts` — check there before adding conditional logic.
- Environment variables are validated at startup in `src/lib/env.ts`. Add new ones there first.

## Architecture Decisions
<!-- Record non-obvious choices so Claude doesn't suggest alternatives -->
- We use server components by default; client components only when interactivity is required.
- Auth is handled via custom middleware in `src/middleware.ts`, not a third-party auth library.
- We chose Prisma over Drizzle for the query builder — don't suggest switching.

## Current Focus
<!-- Update this regularly to give Claude task-level context -->
- Migrating from Pages Router to App Router (70% complete, `/dashboard` routes remain).
- Adding role-based access control to API routes.

## Known Issues / Tech Debt
<!-- Prevents Claude from "fixing" things that are known or intentional -->
- `src/lib/legacy-auth.ts` is deprecated but still used by `/api/v1/*` routes. Don't refactor yet.
- Test coverage is low on `src/services/billing.ts` — tests welcome here.

## Don'ts (Project-Specific)
- Don't modify `prisma/schema.prisma` without asking — migrations affect production.
- Don't add new API routes under `/api/v1/`; use `/api/v2/` for all new endpoints.
- Don't install CSS libraries — we use Tailwind exclusively.
```

### Tips for maintaining it

- Keep it under ~150 lines. If it's too long, Claude pays a context tax on every prompt. Move detailed specs into separate files and reference them with # when needed.
- Update "Current Focus" weekly. This is the highest-value section — it steers Claude toward relevant work without you repeating yourself.
- Use "Don'ts" sparingly. Only add things Claude has actually gotten wrong or that would be costly mistakes. Don't preemptively list every possible error.
- Let Claude maintain it. After finishing a feature or making an architecture decision, tell Claude to update the project CLAUDE.md accordingly.

## Agents
Agents (subagents) are specialized AI assistants that run in their own context window with custom system prompts and scoped tool access. They keep your main conversation clean by offloading exploration, review, or domain-specific tasks into isolated contexts — only returning the final result.

Use `/agents` to create a new Agent for a guided setup, or create a Markdown file manually under: `.claude/agents/code-reviewer.md` (scoped to the project) or `~/.claude/agents/code-reviewer.md` (available across all projects).

When prompting, you can ask a task to use an agent through the `@.claude./agents/agent-name` reference.

Always be cautious about the model used for the specific agent to be cost effective.

Agents are isolated execution contexts with their own prompts, tools, and models. Skills are lazy-loaded context templates (Markdown files with instructions) that get injected into an agent's context. They complement each other: an agent can have skills auto-loaded via the `skills` field.

Pro tip, use Claude to create, manage and update your agents.

Here are existing agents created by the community: https://github.com/dduzgun-security/agents/tree/main/plugins 

## Skills
Think of it as recurring workflow. Skills are essentially templates of context in Markdown format that are lazy loaded only when needed. 

Here is an example of how to create a skill / recurring workflow.

```
user: fetch the latest top 10 startup app ideas on reddit and hacker news with the most attraction

claude: .. outputs the 10 startup app ideas

user: save them on my local claude directory

claude: .. saves it in the local directory as a Markdown file (eg: .claude/top10-startup-ideas-2026-01-01.md)

user: save what we just did into a new skill

claude: .. creates a ./claude/skills/fetch-top10-startup-ideas.md file which illustrates a step-by-step workflow of instructions to execute and also creates a command for it ./claude/commands/fetch-top10-startup-ideas.md invokable through /fetch-top10-startup-ideas
```

Pro tip, use Claude to create, manage and update these skills.  

There are multiple community skills available which can be used: https://github.com/anthropics/skills/tree/main

## Plugins
Plugins are shareable packages that bundle multiple Claude Code extensions (skills, agents, hooks, commands, MCP servers) into a single installable unit. Think of them as the distribution layer, while skills and agents live as individual files, plugins wrap them together for easy sharing across projects and teams.

### Marketplaces
Marketplaces are GitHub repos that host collections of plugins. There are three main sources:

- Anthropic official: anthropics/claude-plugins-official for curated, Anthropic-maintained plugins.
- Anthropic examples: anthropics/claude-code for reference plugins for PR review, security guidance, SDK development.
- Community: Repos like wshobson/agents, claude-plugins.dev, and others with thousands of community-contributed plugins.

```markdown
# Add a marketplace
/plugin marketplace add anthropics/claude-plugins-official

# List available plugins from your marketplaces
/plugin marketplace list
```

### Creating a plugin

The minimal plugin needs a `plugin.json` manifest:
```json
{
  "name": "my-plugin",
  "description": "What this plugin does",
  "version": "1.0.0"
}
```
Then add components as needed. For example, adding a skill:
```
my-plugin/
├── .claude-plugin/
│   └── plugin.json
└── skills/
    └── code-review/
        └── SKILL.md
```

Where SKILL.md follows the standard skill format:
```
---
name: code-review
description: Reviews code for best practices and potential issues.
---

When reviewing code, check for:
1. Code organization and structure
2. Error handling
3. Security concerns
4. Test coverage
```
### Testing locally
Use `--plugin-dir` to load a plugin from a local directory during development without installing it:
```
claude --plugin-dir ./my-plugin
```

### Notable plugins worth trying

- frontend-design: Production-grade UI generation that avoids generic AI aesthetics.
- security-guidance: Security-focused code analysis.
- context7 (MCP): Pulls version-specific documentation from source repos into context.
- Firecrawl (MCP): Web scraping with JS rendering, anti-bot handling, and clean markdown output.
- Playwright: Browser automation for testing flows.
- Repomix: Packs codebases into AI-friendly formats for analysis.

### Cost-conscious tips

- MCP servers in plugins can blow up your context window. Since late 2025, Tool Search lazy-loads MCP tool definitions (dropping context from ~134K to ~5K tokens for large tool libraries), but still audit with `/mcp` and disable what you don't need.
- Prefer plugins with skills over heavy MCP integrations when you just need knowledge/instructions rather than live API access.
- Use `/context` to check how much token budget plugins are consuming.


### Plugins vs. standalone config
Use standalone files in `.claude/` when you're iterating quickly on a single project and don't need to share. Use plugins when you want to distribute skills, agents, hooks, and commands as a single package across projects or teams. 

## My way of working

### `/docs` directory
Before asking Claude Code to do anything, I always create a `/docs` directory which serves as my planing zone. 

### The `PRD.md` (Product Requirements Document)
Once created, I go through a back and forward session with Claude Code to create a `PRD.md` document about the feature I want to implement. I make sure that the following sections are present in my PRD document:

1. **Overview** — What you're building and why it matters, in a few sentences.

2. **Problem Statement** — The user or business problem, backed by data or research.

3. **Goals & Success Metrics** — What success looks like, with measurable targets.

4. **Scope & Non-Scope** — What's included and, just as importantly, what's not.

5. **Requirements** — Functional (what it does) and non-functional (performance, security, etc.).

6. **Open Questions** — Unresolved decisions that need input.

---

> **Optional sections to add for larger initiatives:** Target Users / Personas, Design & UX, Technical Considerations, Dependencies & Assumptions, Release Plan, Risks & Mitigations, Appendix.

example: https://github.com/dduzgun-security/CodeCensus/blob/main/docs/PRD.md

Once the PRD is completed and everything makes sense, I move on to the RFC step.

### The `RFC.md` (Request for Comments)
An RFC (Request for Comments) is more technically focused than a PRD. It's proposing how to build something and inviting feedback from engineers and architects. Here's how a good one flows:

1. **Title & Metadata** — RFC number, author, date, status (Draft / In Review / Accepted / Rejected), stakeholders and reviewers.

2. **Summary** — A one-paragraph explanation of what you're proposing. Someone should be able to read this and know whether the RFC is relevant to them.

3. **Motivation** — Why this change is needed. What's the current state, what's broken or limiting, and what triggered this proposal? Link to the relevant PRD if one exists.

4. **Proposed Design** — The core of the RFC. Describe the architecture, components, APIs, data models, and how they interact. Use diagrams where helpful. Be specific enough that another engineer could implement it.

5. **Alternatives Considered** — What other approaches did you evaluate and why did you reject them? This shows you've thought broadly, not just landed on the first idea.

6. **Trade-offs & Limitations** — Be honest about what this design doesn't solve, where it's weak, or what technical debt it introduces.

7. **Migration / Rollout Plan** — How do you get from here to there? Backward compatibility, feature flags, data migrations, rollback strategy.

8. **Security & Privacy Considerations** — Any new attack surfaces, data handling changes, or compliance implications.

9. **Observability & Operability** — How will you monitor this? Logging, metrics, alerting, and how on-call engineers will debug issues.

10. **Open Questions** — What you haven't decided yet and what you need feedback on. This is where the "Request for Comments" part actually happens.

---

> **Tone:** Direct and technical — not a sales pitch. A great RFC makes it easy for reviewers to poke holes, because that's the whole point.

example: https://github.com/dduzgun-security/CodeCensus/blob/main/docs/RFC.md

### The `prd-rfc-reviewer` sub-agent
I'm not a project manager by any mean, this is why I create a `prd-rfc-reviewer.md` sub-agent to help me review the content I've created before creating the tasks.

example: https://github.com/dduzgun-security/CodeCensus/blob/main/.claude/agents/prd-rfc-reviewer.md

### The task planifier (TASKS.md)
Once the RFC is solid, I don't jump straight into coding. I ask Claude Code to break the RFC down into a TASKS.md file — a sequenced list of implementable units of work. This is the bridge between "what we designed" and "what we build next."

I prompt Claude Code in plan mode (Shift+Tab ×2) with something like:
```
Read the PRD at docs/PRD.md and the RFC at docs/RFC.md. Break the implementation into a sequenced task list. Each task should be small enough to complete in a single Claude Code session that will get cleared once completed. Output it as docs/TASKS.md.
```

What a good TASKS.md looks like
```
# Implementation Tasks

## Phase 1: Foundation
- [x] Task 1: Set up project scaffolding (framework, linting, CI)
- [x] Task 2: Define database schema and run initial migration
- [ ] Task 3: Implement base API structure with health check endpoint

## Phase 2: Core Features
- [ ] Task 4: Build asset CRUD endpoints
- [ ] Task 5: Add ownership assignment logic
- [ ] Task 6: Implement search with filtering

## Phase 3: Polish
- [ ] Task 7: Add notification system
- [ ] Task 8: Build analytics dashboard
- [ ] Task 9: Write end-to-end tests
```

Rules I follow
- One task = one focused session. If a task needs more than ~30 minutes of Claude Code time, it's too big — split it.
- Sequence matters. Tasks should build on each other so you're never blocked by a dependency you haven't built yet.
- Checkboxes are the progress tracker. After completing a task, I tell Claude to check it off in TASKS.md. This way, when I /clear and start a new session, Claude can read TASKS.md and immediately know where I left off.
- Reference the source. Each task should trace back to a requirement in the PRD or a component in the RFC. If a task doesn't map to either, question whether it's needed.
- Re-plan when things change. If midway through implementation the design shifts, I update the RFC first, then regenerate or adjust TASKS.md. The task list is a living document, not a contract.


The workflow in practice
```
1. /clear
2. Read docs/TASKS.md, pick up from the first unchecked task.
3. Complete the task.
4. Run tests and lint to verify.
5. Mark the task as done in TASKS.md.
6. Commit with a clear message tied to the task.
7. /clear and repeat.
```

This keeps each session focused, the context window clean, and gives you a clear record of progress. It also means you can stop and resume at any point. The state lives in the repo, not in your head or a Claude Code session.