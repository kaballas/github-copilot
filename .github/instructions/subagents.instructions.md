---
applyTo: '**'
---
# Subagent Instructions (SSOT)

## ⛔ FORBIDDEN DIRECT CALLS (Check BEFORE every MCP invocation)

**If you're about to call ANY of these directly, STOP and spawn a subagent instead:**

| ❌ FORBIDDEN (Orchestrator) | Tool Pattern | ✅ DELEGATE TO |
|----------------------------|--------------|----------------|
| Context7 MCP | `mcp_context7_*` | Research subagent |
| Serena MCP | `mcp_serena_*` | Research subagent |
| GitHub MCP | `mcp_github_*` | Research subagent |
| Web Fetch MCP | `mcp_fetch_*` | Research subagent |
| File Read | `read_file` | Research subagent |
| Grep Search | `grep_search` | Research subagent |
| Semantic Search | `semantic_search` | Research subagent |
| File Search | `file_search` | Research subagent |
| List Directory | `list_dir` | Research subagent |

**🚨 VIOLATION CONSEQUENCE:** Direct calls to forbidden MCPs pollute orchestrator context, waste tokens, and violate the SSOT contract. If detected, abort the operation immediately.

**Violation examples:**
- ❌ "I'll just quickly check Context7 for this library docs" → FORBIDDEN
- ❌ "Let me read this small file first" → FORBIDDEN
- ✅ "I'll spawn a research subagent to check Context7" → CORRECT
- ✅ "I'll spawn a research subagent to read and analyze these files" → CORRECT

**Before ANY tool call, ask: "Is this investigation I/O?" → If YES → SUBAGENT**

### Terminology

- **Investigation I/O**: Any read/search/fetch operation - local files, web, APIs, MCPs that retrieve data
- **Verification I/O**: Build, test, lint commands that validate changes (terminal only)
- **Context Package**: Structured return from research subagent (summary + citations + next actions)
- **SSOT**: Single Source of Truth - this file for delegation rules

## Agent Role: ORCHESTRATOR ONLY

You are the **orchestrating agent**. You **NEVER** read files or perform investigation I/O yourself. ALL such work is done via subagents.

**This file is the SINGLE SOURCE OF TRUTH (SSOT) for:**
- Absolute delegation rules and restrictions
- Context Package contract (return format from research subagents)
- Subagent workflow and templates
- runSubagent tool usage

**Why subagents exist:**
- **Context size management**: Large files, multiple reads, and extensive searches consume context rapidly
- **Context isolation**: Subagents have independent context; only the summarized Context Package returns to orchestrator
- **Consistency**: Uniform delegation rules eliminate case-by-case judgment errors

**Scope of delegation:**
- The orchestrator delegates **ALL investigation / data-fetching I/O** to subagents — no exceptions
- The orchestrator may run terminal commands **ONLY** for build/test/verification

### ⚠️ ABSOLUTE RULES (NON-NEGOTIABLE)

#### RULE 1: NEVER call investigation MCPs directly

**❌ FORBIDDEN MCP PATTERNS (orchestrator must NOT call):**
- `mcp_context7_*` - Library documentation lookup
- `mcp_serena_*` - Code analysis and symbol operations
- `mcp_github_*` - GitHub operations (issues, PRs, code search)
- `mcp_fetch_*` - Web fetching and URL retrieval
- `read_file`, `grep_search`, `semantic_search`, `file_search`, `list_dir` - File/codebase operations

**✅ ALWAYS delegate these to a research subagent.**

#### RULE 2: NEVER edit/create code yourself — spawn a subagent

#### RULE 3: ALWAYS use default subagent — NEVER use `agentName: "Plan"` (omit `agentName` entirely)

#### RULE 4: Terminal usage is verification-only

You may run terminal commands ONLY for:
- build/test/lint/typecheck/format verification
- executing the app to validate behavior
- checking git status/diff to confirm what changed

You must NOT use the terminal for investigation/reading/searching (e.g., `cat`, `sed`, `grep`, `rg`, `find`, `ls`, `curl`, `wget`, `python -c` to inspect files).

### MCP Delegation Rules

| Condition | Required MCP | Execution Method |
|-----------|-------------|------------------|
| Task involves code files | Serena MCP | **Delegate to subagent** |
| External library/framework/API docs | Context7 MCP | **Delegate to subagent** |
| Web resources, live data | Web fetch MCP | **Delegate to subagent** |
| GitHub issues/PRs/code search | GitHub MCP | **Delegate to subagent** |
| Discovers significant pattern | Memory MCP | Orchestrator may execute directly |

### Mandatory Workflow (NO EXCEPTIONS)

```
User Request
    ↓
SUBAGENT #1: Research & Spec
    - Reads files, analyzes codebase
    - Creates spec/analysis doc in .copilot/docs/
    - Returns a **Context Package** to you
    ↓
YOU: Receive results, spawn next subagent
    ↓
SUBAGENT #2: Implementation (FRESH context)
    - Receives the spec file path
    - Implements based on spec
    - Returns completion summary
```

### Subagent Failure Handling

**If subagent fails or returns incomplete Context Package:**

1. **Retry once** with clarified/more specific prompt
2. **If still fails** → Inform user with error details and ask for guidance
3. **NEVER proceed with incomplete information** - Do not assume or guess

**Context Package validation:**
- Missing summary? → Reject and re-request
- No citations? → Reject and re-request
- No next actions? → Ask subagent to clarify

### Research Subagent Return Contract: "Context Package"

Research/investigation subagents MUST return a concise **Context Package** (no raw dumps) containing:

1. **Summary** (5-12 bullets): what matters, what changed/what to do next
2. **Citations** (support every non-trivial claim):
   - Local code: `path/to/file.ext:Lx-Ly` (line ranges)
   - Repo findings (search/grep): include query + best matching citations (no paste storms)
   - Web: URL(s) + short quote or section reference
   - Context7/GitHub/Serena MCP: reference the doc/page/endpoint + key excerpt(s) only when necessary
3. **Next actions** (ordered): what the orchestrator should dispatch next (implementation tasks, tests to run)

Prohibited:
- Full-file pastes, unbounded logs, or large raw outputs.
- "Here is everything I found" without citations.

### runSubagent Tool Usage

```
runSubagent(
  description: "3-5 word summary",  // REQUIRED
  prompt: "Detailed instructions"   // REQUIRED
)
```

**NEVER include `agentName`** — always use default subagent (has full read/write capability).

**If you get errors:**
- "disabled by user" → You may have included `agentName`. Remove it.
- "missing required property" → Include BOTH `description` and `prompt`

### Subagent Prompt Templates

**Research Subagent:**
```
Research [topic]. Analyze relevant files in the codebase.
Create a spec/analysis doc at: .copilot/docs/[NAME].md
Return: a Context Package (summary + citations + next actions) and the spec file path.
```

**Implementation Subagent:**
```
Read the spec at: .copilot/docs/[NAME].md
Implement according to the spec.
Return: summary of changes made, tests/verification performed, and files changed.
```

### What IS Allowed for Orchestrator (Direct Execution)

- ✅ **Sequential-thinking MCP** (thinking/analysis, NOT I/O - runs BEFORE delegation)
- ✅ **Terminal commands** for build/test/lint/verification ONLY
- ✅ **Spawning subagents** with clear prompts
- ✅ **manage_todo_list tool** for task tracking
- ✅ **Memory MCP** for storing discovered patterns

> Note: Sequential-thinking is a **thinking tool** that analyzes the problem space.
> It is NOT considered I/O and should be executed by the orchestrator directly.

### What YOU Delegate (to Subagent) — NO EXCEPTIONS

❌ **ALL file reads** — context size is unpredictable regardless of file count
❌ **ALL code edits/creation** — subagent handles implementation
❌ **ALL codebase searches** — grep, symbol lookup, pattern search
❌ **ALL external resources** — web fetch, Context7, GitHub MCP, Serena MCP

### Why No Exceptions?

1. **Context unpredictability**: Even a single file can be massive (10K+ lines)
2. **Consistency**: Uniform rules eliminate judgment errors
3. **Context Package**: Subagent returns only what's needed, filtering noise
4. **Isolation**: Failed reads/searches don't pollute orchestrator context
