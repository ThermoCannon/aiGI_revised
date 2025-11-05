Absolutely—you can link a public GitHub repository and I’ll review it directly from here.

You asked me to **review your repo and produce a comprehensive, implementation‑ready change plan** that reworks your aiGI system around your new aims. I’ve analyzed the repository you provided:

> **Repo:** `ThermoCannon/aiGI_revised` (forked from `ruvnet/rUv-dev`) ([GitHub](https://github.com/ThermoCannon/aiGI_revised/tree/main/aiGI "aiGI_revised/aiGI at main · ThermoCannon/aiGI_revised · GitHub"))

Below is a detailed technical report and an execution roadmap that another AI coding agent (Roo Code modes using Gemini 2.5 Pro) can follow to implement and push the changes.

> **What I was able to read in the repo (key artifacts):**
>
> • `roo_mode_defaults/system_prompt.md` – current system prompt and tool rules (notably: “exactly one tool per message” & “every assistant message must include a tool call”). ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))
>
> • `.roomodes` – current custom modes (architect, code/auto‑coder, tdd, debug, security-review, docs-writer, integration, post-deployment-monitoring-mode, refinement-optimization-mode, ask, devops, tutorial, supabase-admin). ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roomodes?raw=1 "raw.githubusercontent.com"))
>
> • `.roo/README.md` – Roo modes & MCP integration overview. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/README.md?raw=1 "raw.githubusercontent.com"))
>
> • `.roo/mcp.json` – MCP servers (Supabase, Mem0, PerplexityAI) with Composio endpoints. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))
>
> • `templates/minimal-roo/.roo/README.md` – minimal Roo mode framework structure and extension points (rules, rules-code, `mcp.json`, `.roomodes`). ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/templates/minimal-roo/.roo/README.md?raw=1 "raw.githubusercontent.com"))
>
> • `package.json` – `name: create-sparc`, `version: 1.2.2`, executable at `bin/index.js`, tests via Jest, lint via ESLint, files that get published. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/package.json?raw=1 "raw.githubusercontent.com"))
>
> • `bin/index.js` – entry that calls `src/cli`. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/bin/index.js?raw=1 "raw.githubusercontent.com"))
>
> • `src/cli/index.js` – registers commands: `init`, `add`, `help`, `wizard`, `configure-mcp`, `aigi`, `minimal`. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/src/cli/index.js?raw=1 "raw.githubusercontent.com"))
>
> • `plans/README.md` and `plans/phases/*` – a complete architecture/spec/refinement/completion blueprint for the `create-sparc` NPX. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/plans/README.md?raw=1 "raw.githubusercontent.com"))

Where the GitHub UI blocked directory listings (e.g., `aiGI/` and `clinerules-bank/orchestration/`), I inferred structure from the root listing and existing templates. The plan below still includes precise file‑level changes for those areas so a coding agent can implement them deterministically once it opens those paths locally.

---

## Executive summary

**Your new aims** require: (1) **Entrypoint‑centric orchestration** (multiple “entrypoint” modes, each with a curated workflow), (2) **robust codebase analysis** that fuses docs + implementation, (3)  **reproducible, guardrailed behavior** , and (4) **multi‑session continuity** with strong session documentation.

**Current repo capabilities** (SPARC scaffolding, Roo modes, MCP wiring) are a strong base, but the **default Roo prompt and modes** do not yet implement your specific entrypoint orchestration, logging, session pick‑up, and debugger/memory manager upgrades. The **system prompt** also **forces one‑tool‑per‑message** and  **tool call every message** , which reduces autonomy; we’ll replace that with a  **more permissive and stall‑proof prompt** . ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))

**What this plan delivers:**

1. **Entrypoint mode suite** : `entry-data-exploration`, `entry-scripting`, `entry-deliverable-tools`, `entry-project-maintenance`, `entry-fullstack`—each orchestrating a tailored set of modes and rules. (These are separate from, but coordinate with, your existing `architect`, `code`, `debug`, etc., and include a new `orchestrator` override slug that supersedes Roo’s default Orchestrator.)
2. **Mode consolidation** : fold “rUvNet flavor” modes (tutorial, prompt-generator, MCP integrator, SPARC orchestrator, aiGI orchestrator, system integrator, optimizer, dev ops, deployment monitor, Supabase) into a **clean set** with clear ownership and hand‑off.
3. **Upgraded system prompt** : removes “one tool per message / tool required every message”, adds **dynamic workspace infusion** (folder map, cwd), adds  **fail‑safe handback to entrypoint orchestrator** , improves **apply_diff discipline** (re‑read before diff), and **stall preemption** (auto next‑step decisions).
4. **Logging & session persistence** : every mode logs to `logs/aiGI/<date>/<mode>.log` (structured JSON lines); a `session-startstop` mode writes durable session summaries into `docs/sessions/…`, and the orchestrator reads the latest logs at each decision point.
5. **Debugger & memory manager upgrades** : debugger uses a **differential diagnostics loop** (collect, hypothesize, isolate, verify, patch); memory manager guides **local DB setup** and  **codebase embedding** , including **index retrieval** and **optional Ollama local embedder** integration.
6. **MCP expansion** : add **Perplexity MCP, Context7 MCP, Serena MCP, GitHub MCP, Markitdown MCP** and gate them by mode purpose; keep Supabase/Composio where present. (Your current `.roo/mcp.json` has Supabase, Mem0, PerplexityAI via Composio.) ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))
7. **CLI simplification** : collapse installers into **one “aiGI revised” installer** (`create-sparc aigi-revised setup`), which overlays: the new `.roomodes`, `.roo/rules-*`, `.roo/mcp.json`, prompt override, docs and scripts.
8. **Hardening for reproducibility** : consistent project structure, CI checks, test harness and acceptance criteria (unit + integration tests).

Where helpful, I reference Roo Code docs on  **custom instructions / prompt structure** , confirming your intended override mechanisms. ([Roo Code Docs](https://docs.roocode.com/features/custom-instructions/?utm_source=chatgpt.com "Custom Instructions | Roo Code Documentation"))

---

## Current state: gaps vs. your aims

### Modes & orchestration

* **Present** : rich set of modes in `.roomodes`, but **no `orchestrator` slug override** exists yet (so Roo’s default Orchestrator behavior persists). ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roomodes?raw=1 "raw.githubusercontent.com"))
* **Gap** : Need **entrypoint orchestrators** mapping to your five workflows, plus an **override for the default `orchestrator` slug** to centralize hand‑offs, logging, and fail‑safes.

### System prompt & autonomy

* **Present** : `roo_mode_defaults/system_prompt.md` mandates **exactly one tool per message** and requires a tool call **every** message. That’s rigid, and it risks stalls. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))
* **Gap** : Replace with a **permissive, stall‑resistant** prompt that (a) allows multi‑tool or no‑tool messages, (b) dynamically embeds workspace/folder map and cwd, (c) formalizes **apply_diff** discipline, (d) enforces **failsafe handback** to the current entrypoint orchestrator.

### Logging & session continuity

* **Present** : No standard per‑mode logging schema or session start/stop writer.
* **Gap** : Add log spec, directories, and rules. Add session modes that “pick up where we left off.”

### Debugging & memory

* **Present** : A `debug` mode exists but needs  **systematic differential diagnostics** . ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roomodes?raw=1 "raw.githubusercontent.com"))
* **Gap** : Improved instructions and loop; add **memory-manager** instructions to configure **local DB** or  **Mem0** /**Ollama** embeddings; add rules for retrieving Roo’s  **codebase indexing** .

### MCP

* **Present** : `.roo/mcp.json` wires  **Supabase** ,  **Mem0** ,  **PerplexityAI (Composio)** . ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))
* **Gap** : Add  **Context7** ,  **Serena** ,  **GitHub** , **Markitdown** and scope usage by mode.

### CLI & installer

* **Present** : `create-sparc` NPX with `init`, `aigi`, `minimal`, etc. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/src/cli/index.js?raw=1 "raw.githubusercontent.com"))
* **Gap** : Deliver **one consolidated “aiGI revised” installer** that updates `.roomodes`, `.roo`, rules, MCP, docs, and prompt with minimal choices.

---

## Target design

### A. Entrypoint workflow modes (new)

Each entrypoint mode is an orchestrator with a **pre‑configured workflow** (curated sub‑modes, rules, MCP access, and documentation discipline). They all share the same **core orchestration rule set** but differ in included tools/modes and “when‑to‑use.”

1. **`entry-data-exploration`**

   **Purpose:** infer schemas from flat files/DBs, connect via Postman/HTTP, validate/visualize, create tools that run inside data pipelines.

   **Includes:** `code` (Python/TypeScript bias), `docs-writer`, `debug`, `data-scientist` (new), `web-scraper` (new), `environment-manager` (new).

   **MCP:** Markitdown (scrapes & cleans pages into Markdown), Perplexity, Context7 for literature/background, GitHub (if repo introspection needed).
2. **`entry-scripting`**

   **Purpose:** fast one‑off scripting in existing workspaces; tight refactor/run loops; verify via outputs against user standards.

   **Includes:** `code` (Brute‑code flavor), `debug`, `docs-writer`, `session-startstop`.

   **Behavior:** favors  **runtime feedback** , minimal ceremony.
3. **`entry-deliverable-tools`**

   **Purpose:** robust, shareable tools;  **small‑scale TDD** , packaging, reviewer‑scorer‑critic cycle.

   **Includes:** `code`, `tdd`, `debug`, `docs-writer`, `security-review`, `packaging` (new), `session-startstop`.
4. **`entry-project-maintenance`**

   **Purpose:** enforce project organization, docs, guardrails, GitHub repo hygiene; includes GitHub issue/PR automation; ensures `.roo`/rules are in sync.

   **Includes:** `project-setup` (new), `docs-writer`, `orchestrator` core spec, `github-maintenance` (new), `environment-manager`, `session-startstop`.
5. **`entry-fullstack`**

   **Purpose:** the heavyweight, autonomous option; formal  **TDD** , early **Architecture/Vision** emphasis, project phases and reviewer‑scorer‑critic.

   **Includes:** `visionary` (new), `architect`, `code`, `tdd`, `debug`, `security-review`, `docs-writer`, `memory-manager`, `integration`, `optimizer`, `session-startstop`.

> **Note:** These entrypoints don’t replace `code`, `debug`, etc.; they **orchestrate** them. We will also **override Roo’s default `orchestrator` slug** with a generalized orchestrator that can **launch any entrypoint** and serve as the  **failsafe sink** .

### B. Mode consolidation

* **Consolidate** tutorial/prompt-generator/MCP integrator/SPARC orchestrator/aiGI orchestrator/system integrator/optimizer/devops/deployment monitor into:

  `visionary`, `project-setup`, `github-maintenance`, `optimizer`, `devops`, `integration`.
* Keep `security-review`, `docs-writer`, `supabase-admin`, but scope them under entrypoints and  **improve instructions** .

### C. System prompt rework (high‑level)

Replace constraints in `roo_mode_defaults/system_prompt.md`:

* Remove/replace **“exactly one tool per message”** and **“every message must include a tool call”** with:
  * “Use tools  **as needed** . Multi‑step tool use within a message is permitted. It’s also acceptable to reply without tools when summarizing or deciding next steps.”
* Add **dynamic context** section injected at runtime:
  * Current workspace directory
  * Brief folder map (top‑level + key subfolders)
  * Any found **rules** + **.roomodes** diff markers
* Add **stall‑prevention** policy: “If a tool fails or insufficient info, **propose next best action and proceed** (don’t halt).”
* Add  **apply_diff discipline** : “Re‑read file before diff; include precise search/replace blocks; avoid partial mismatches.”
* Add  **failsafe** : “On task end or uncertainty, **hand control back to the active entrypoint orchestrator** with a one‑line status and next suggestion.”
* Add **logging** obligations (see next section).

> Roo’s prompt layering & custom instructions mechanism supports these overrides. ([Roo Code Docs](https://docs.roocode.com/advanced-usage/prompt-structure?utm_source=chatgpt.com "Prompt Structure | Roo Code Documentation"))

### D. Logging & session specification

**Directory structure**

```
logs/aiGI/YYYY-MM-DD/{mode-slug}.log.ndjson
docs/sessions/YYYY-MM-DD/HHMM-session-{entrypoint}.md
```

**Log schema (per line, NDJSON)**

```json
{
  "ts": "2025-11-05T16:20:15.230Z",
  "entrypoint": "entry-fullstack",
  "mode": "debug",
  "task": "isolate flaky test",
  "action": "execute_command",
  "summary": "Ran unit tests, captured failing seed",
  "artifacts": ["__tests__/unit/foo.test.ts"],
  "status": "ok",
  "next": "refactor module A, rerun tests"
}
```

**Rules**

* Every mode **must** log start/end with `status` and `next`.
* Entrypoint orchestrators **read the last 50 lines** from all session logs before decisions.
* The **Session Start/Stop** mode:
  * **Start:** reads recent logs, creates `docs/sessions/…/start.md` summarizing context + goals.
  * **Stop:** writes `…/end.md` (what changed, artifacts, open TODOs) and **prompts the orchestrator** to persist the next steps checklist.

### E. Debugger & memory manager upgrades

**Debugger (improved loop)**

1. **Collect** : run to error, capture stack/logs, record reproduction steps.
2. **Hypothesize** : enumerate likely causes; pick top two.
3. **Isolate** : add targeted logs/tests; shrink repro.
4. **Patch** : minimal, reversible change; re‑run; compare metrics.
5. **Stabilize** : write regression test; document in log; hand back to entrypoint with status.

**Memory manager**

* Guides setup of **local DB** (SQLite or Postgres) for persistent memory (optionally **Mem0** as cloud memory).
* Instructions for **codebase embeddings** and retrieval (Roo’s codebase indexing & local embedder via  **Ollama** ).
* Rules: never store secrets in repo; describe `.env` boilerplate and seeding commands.

### F. MCP expansion & gating

**Update `.roo/mcp.json`** to include gated servers:

* **GitHub MCP** (repo maintenance from `entry-project-maintenance` and `github-maintenance` mode),
* **Markitdown MCP** (webpage → Markdown for `entry-data-exploration` & `docs-writer`),
* **Context7 MCP** and **Serena MCP** (research/brainstorming for `visionary` & `entry-fullstack`),
* Keep  **PerplexityAI** / **Mem0** /**Supabase** present. Current file shows Supabase, Mem0, PerplexityAI. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))

---

## Concrete changes (file‑by‑file)

> The diffs below are **surgical, anchor‑based** so another agent can apply them reliably after reading each target file content.

### 1) Override Roo’s defaults & install entrypoints

**File:** `.roomodes` (JSON)

**Action:** Add/replace entries:

* **Add** a **universal orchestrator** that **uses the default Roo slug** (`"slug": "orchestrator"`) so it overwrites Roo’s default. It must (a) present entrypoint choices, (b) log decisions, (c) read recent mode logs, (d) be the **failsafe** recipient.
* **Add** five **entrypoint** modes with slugs:
  * `entry-data-exploration`
  * `entry-scripting`
  * `entry-deliverable-tools`
  * `entry-project-maintenance`
  * `entry-fullstack`
* **Add** the new specialist modes:
  * `environment-manager`
  * `visionary`
  * `session-startstop`
  * `data-scientist`
  * `brute-code`
  * `project-setup`
  * `github-maintenance`
  * `web-scraper`
  * `packaging`
  * `memory-manager`

**Patch (append to `customModes` array)** — **example entries** (trim for brevity; keep style consistent with current file) ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roomodes?raw=1 "raw.githubusercontent.com"))

```json
{
  "slug": "orchestrator",
  "name": "🧭 Entrypoint Orchestrator",
  "roleDefinition": "You present entrypoint choices, orchestrate workflows, review recent logs, and ensure fail-safe handoffs.",
  "customInstructions": "At start, offer: data-exploration, scripting, deliverable-tools, project-maintenance, fullstack. Before each decision, read last 50 log lines across modes. On task end or uncertainty, hand control back to yourself with a 1-line status+next. Enforce apply_diff discipline (re-read before diff).",
  "groups": ["read","edit","browser","mcp","command"],
  "source": "project"
},
{
  "slug": "entry-data-exploration",
  "name": "🔍 Entry: Data Exploration",
  "roleDefinition": "You explore flat files/databases, validate/visualize data, and build pipeline-ready tools.",
  "customInstructions": "Prefer python+pandas notebooks/tools. Use Markitdown MCP for web->md. Create validation reports and simple dashboards. Always write logs and session docs.",
  "groups": ["read","edit","browser","mcp","command"],
  "source":"project"
},
{
  "slug": "environment-manager",
  "name": "📦 Environment Manager",
  "roleDefinition": "You provision and verify virtual envs, lock dependencies, and check active venv before running.",
  "customInstructions": "Create scripts in scripts/env/. Validate python/node toolchains; write README updates.",
  "groups": ["read","edit","command"],
  "source":"project"
},
{
  "slug": "visionary",
  "name": "🧠 Visionary",
  "roleDefinition": "You brainstorm roadmaps, diagrams, alternatives; produce planning docs and mockups.",
  "customInstructions": "Use Context7/Serena/Perplexity MCP for research. Output structured planning docs to docs/vision/.",
  "groups": ["read","edit","browser","mcp"],
  "source": "project"
},
{
  "slug": "session-startstop",
  "name": "⏱ Session Start/Stop",
  "roleDefinition": "You write session summaries and 'pick-up-where-we-left-off' docs.",
  "customInstructions": "On start, read latest logs; on stop, summarize artifacts, TODOs, next-step checklist in docs/sessions/.",
  "groups": ["read","edit"],
  "source": "project"
}
```

*(Repeat for the remaining entrypoints and specialists, following your policy text.)*

**Acceptance criteria**

* Roo shows **orchestrator** with the new name and choices.
* Choosing an entrypoint restricts tools per its instructions.
* On **any** unresolved state, control returns to `orchestrator`.

---

### 2) Rework system prompt for autonomy & guardrails

**File:** `roo_mode_defaults/system_prompt.md`

**Observed anchors:** file begins with “You are Roo…” and contains **“TOOL USE”** and **“## Tools”** sections with strict rules. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))

**Actions (anchor‑based edits)**

* **Replace** the “==== TOOL USE” section with a **Tool Use Policy** that:
  * allows **multi‑tool or no‑tool** replies,
  * forbids stalls (“propose next action and proceed”),
  * requires **re‑read** before `apply_diff`,
  * compels **logging** and **failsafe** handback to Orchestrator.
* **Insert** a new “==== DYNAMIC WORKSPACE CONTEXT” section near the top that instructs the agent to include:
  * cwd,
  * top‑level folder map,
  * critical `.roo`/`.roomodes` paths,
  * a short “recent changes” note if available.

*Illustrative replacement block (keep your style and headings):*

```md
==== TOOL USE (REVISED)

Use tools as needed. You may invoke multiple tools in a single message, or none when summarizing/deciding. Never stall: if a tool fails or inputs are incomplete, propose the next best action and proceed.

Before any apply_diff: re-read the file to obtain the current version and use exact search/replace blocks.

Every mode must log actions (start/end/status/next) to `logs/aiGI/YYYY-MM-DD/{mode}.log.ndjson`.

At task end or uncertainty, return control to the active entrypoint orchestrator with:
- one-line status
- next actionable suggestion
```

*Insert after the opening role statement:*

```md
==== DYNAMIC WORKSPACE CONTEXT
At the start of each major task, include:
- current working directory
- summarized folder map (top-level + key subfolders)
- detected .roo/.roomodes presence and notable rule files
- any recent changes noted in logs
```

**Acceptance criteria**

* Prompt no longer forces “one tool per message.”
* Orchestration and logging are mandated by the prompt.
* Agents include dynamic workspace info and respect the apply_diff discipline.

---

### 3) Add rules & documentation to `.roo/`

**Files to add (new):**

```
.roo/
  rules-common/logging.md
  rules-common/apply-diff-discipline.md
  rules-orchestrator/core.md
  rules-orchestrator/entry-data-exploration.md
  rules-orchestrator/entry-scripting.md
  rules-orchestrator/entry-deliverable-tools.md
  rules-orchestrator/entry-project-maintenance.md
  rules-orchestrator/entry-fullstack.md
  rules-debugger/improved-diagnostics.md
  rules-memory/manager.md
  rules-indexing/codebase-indexing.md
```

**Content highlights**

* **logging.md** – NDJSON schema & examples; reading recent logs before decisions; prohibited PII/secrets.
* **apply-diff-discipline.md** – always re‑read; match exact blocks; avoid overlapping separators.
* **core orchestrator** – log review loop; fail‑safe handback; how to pick sub‑modes; when to escalate.
* **entry‑* files* * – each with “Purpose, Included Modes, Allowed MCP, Default Next‑steps, Exit Criteria.”
* **debugger** – five‑step diagnostic loop; standardized triage template.
* **memory manager** – local DB setup (SQLite/Postgres), Mem0 option, Ollama embeddings, how to retrieve Roo index.
* **indexing** – how to query Roo’s code index and map symbols to files (document the tool/command trigger you use in your environment).

*(Roo loads `.roo/rules` recursively and merges with custom instructions, per docs.)* ([GitHub](https://github.com/RooCodeInc/Roo-Code-Docs/blob/main/docs/features/custom-instructions.md?utm_source=chatgpt.com "Roo-Code-Docs/docs/features/custom-instructions.md at main - GitHub"))

**Acceptance criteria**

* Rules appear in system prompt content (you’ll see their text influence in Roo runs).
* Orchestrators obey the log‑review loop and exit criteria.

---

### 4) Expand & gate MCP servers

**File:** `.roo/mcp.json`

**Current:** Supabase (command), Mem0 (URL), PerplexityAI (Composio URL). ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))

**Action:** Append new servers (illustrative):

```json
{
  "mcpServers": {
    "supabase": { "...": "existing" },
    "mem0": { "...": "existing" },
    "perplexityai": { "...": "existing" },

    "github": {
      "url": "https://mcp.composio.dev/github/<your-token-or-agent>",
      "alwaysAllow": ["issues_list","pulls_list","repos_get","pulls_create","issues_create"]
    },
    "markitdown": {
      "url": "https://mcp.composio.dev/markitdown/<agent>",
      "alwaysAllow": ["fetch","convert","extract"]
    },
    "context7": { "url": "https://mcp.provider/context7/<agent>" },
    "serena":   { "url": "https://mcp.provider/serena/<agent>" }
  }
}
```

**Rules gating (in the new entrypoint rule files):**

* `visionary`, `entry-fullstack`: may use Context7/Serena/Perplexity.
* `entry-data-exploration`: may use Markitdown for scraping; may not create external resources.
* `entry-project-maintenance`: can use GitHub MCP (read & hygiene tasks).
* `supabase-admin`: unchanged for DB ops.

**Acceptance criteria**

* Roo shows these MCPs under appropriate modes; unauthorized modes should not call them.

---

### 5) Add new specialist modes (rules + docs)

* **Environment Manager** : creates `scripts/env/setup_python.sh`, `setup_node.sh`, checks active venv, pins dependencies.
* **Session Start/Stop** : implements session docs layout and links to logs.
* **Visionary** : planning docs + diagrams (Mermaid), exploration checklists.
* **Data Scientist** : pandas/ggplot/matplotlib; makes notebooks under `notebooks/` or scripts under `tools/data/`.
* **Brute Code** : “fast-and-furious” coding loop (read‑run‑refactor), explicit contrast with TDD.
* **Project Setup** : validates `.roo`/`.roomodes`, README tables of contents, docs stubs; synchronizes rule files.
* **GitHub Maintenance** : labels, PR templates, stale issues, branch protection *advice* (no admin APIs unless configured).

**Acceptance criteria**

* Modes appear in `.roomodes` and their rule files exist.
* Running the orchestrator offers these modes when appropriate.

---

### 6) CLI: single installer & maintenance commands

**File:** `src/cli/index.js` shows command registration entrypoint. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/src/cli/index.js?raw=1 "raw.githubusercontent.com"))

**Add new command:** `aigi-revised` with subcommands:

* `setup` – overlays the new modes/rules/MCP/system prompt; offers to back up current `.roo`, `.roomodes`.
* `doctor` – checks presence/consistency of `.roo`, `.roomodes`, MCP endpoints, and rules.
* `upgrade` – re‑applies templates and merges conflicts (preserving local edits with `.bak`).

**Templates to add under `templates/`**

* `aigi-revised/.roo/**` (all rule files above)
* `aigi-revised/.roomodes` (pre‑populated with new entries)
* `aigi-revised/roo_mode_defaults/system_prompt.md` (revised prompt)
* `aigi-revised/docs/` (session scaffolds, vision docs)

**Acceptance criteria**

* `npx create-sparc aigi-revised setup` overlays files and prints a summary of changes.
* `create-sparc aigi-revised doctor` passes after setup.

---

### 7) Codebase analysis mode

**Add** a `codebase-analysis` mode (or fold into `entry-fullstack`) that:

* uses `list_files`, `list_code_definition_names`, and `read_file` in **batches** (respecting the 5‑file cap) to map the repository,
* produces **Architecture & Docs** under `docs/codebase/` with module interdependency diagrams,
* generates **API & config inventories** and a **“Proceed with…” recommendation** that picks the proper entrypoint.

**Acceptance criteria**

* Produces `docs/codebase/summary.md` and `docs/codebase/deps.mmd` (Mermaid) from a fresh run.

---

### 8) Reproducibility & tests

**CI additions**

* Ensure `npm test` runs unit + integration tests (Jest already configured). ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/package.json?raw=1 "raw.githubusercontent.com"))
* Add tests that:
  * parse `.roomodes` and ensure required slugs exist,
  * verify `.roo/rules-*` presence,
  * lint rule files for “forbidden phrases” (e.g., “hardcode secret”).

**Acceptance criteria**

* A clean CI on first PR adding these features; test names make failures diagnosable.

---

## Step‑by‑step roadmap (PR sequence)

> Each PR contains code changes, docs updates, and a short migration note. Keep PRs reviewable.

### PR 0 – Repository hygiene (baseline)

* Add `CONTRIBUTING.md`, `SECURITY.md`, `CHANGELOG.md`.
* Add `docs/ARCHITECTURE.md` that links to `plans/` (already present). ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/plans/README.md?raw=1 "raw.githubusercontent.com"))

### PR 1 – Entrypoint orchestration & `.roomodes`

* Create/append `.roomodes` entries for: `orchestrator`, five `entry-*` modes, specialists (env manager, visionary, etc.). (See JSON examples above.) ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roomodes?raw=1 "raw.githubusercontent.com"))
* Add rules under `.roo/rules-*`.

### PR 2 – System prompt overhaul

* Replace “TOOL USE” block and add “DYNAMIC WORKSPACE CONTEXT” in `roo_mode_defaults/system_prompt.md`. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))
* Add `docs/prompt/CHANGELOG.md` capturing policy changes.

### PR 3 – Logging & session modes

* Add logging rules & schema; add `session-startstop` mode; create directories under `docs/sessions/` and `logs/aiGI/` in template.

### PR 4 – Debugger & memory manager

* Add `rules-debugger/improved-diagnostics.md` and `rules-memory/manager.md`.
* Add helper scripts under `scripts/memory/` and `scripts/env/` (python/node environment checks).

### PR 5 – MCP expansion

* Update `.roo/mcp.json` (GitHub, Markitdown, Context7, Serena), gated by rules. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))
* Add docs on which modes may use which MCPs.

### PR 6 – CLI single‑installer

* Add `aigi-revised` commands to `src/cli` and templates under `templates/aigi-revised/**`. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/src/cli/index.js?raw=1 "raw.githubusercontent.com"))
* Implement backup/overlay behavior and `doctor` validations.

### PR 7 – Codebase analysis mode

* Add `codebase-analysis` mode + rules; manifest output under `docs/codebase/`.

### PR 8 – Multi‑session continuity

* Ensure orchestrator always invokes `session-startstop` at start/end of new session; link log summaries into the session docs.

### PR 9 – Hardening & docs

* Expand tests (presence & sanity checks), performance notes, and “How to select an entrypoint” docs.

---

## Acceptance tests (what “done” looks like)

1. **Installer:** `npx create-sparc aigi-revised setup` overlays files; `doctor` passes. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/src/cli/index.js?raw=1 "raw.githubusercontent.com"))
2. **Orchestrator:** on first prompt, presents the five entrypoints; after each mode end, control returns to Orchestrator with 1‑line status.
3. **Logging:** after a small task, `logs/aiGI/YYYY-MM-DD/` contains NDJSON logs; `docs/sessions/...` contains session start & end docs.
4. **Debugger:** produces differential diagnosis log and regression test on bug fix.
5. **Memory manager:** sets up local DB or Mem0/Ollama instructions; writes `docs/memory/setup.md`.
6. **MCP:** only allowed modes can call GitHub/Markitdown/etc.; calls succeed with configured tokens.
7. **Codebase analysis:** produces `docs/codebase/summary.md` and `deps.mmd` on current repo.
8. **Prompt:** agents are not forced to use exactly one tool each message; no stalls on tool failures. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))

---

## Implementation snippets (ready to paste)

> These are **templates** an AI coder can adapt when editing your repo locally.

**Append to `.roomodes` (partial example; keep JSON valid):** ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roomodes?raw=1 "raw.githubusercontent.com"))

```json
{
  "slug": "entry-project-maintenance",
  "name": "🧹 Entry: Project Maintenance",
  "roleDefinition": "You enforce project structure, docs, rules, and repo hygiene.",
  "customInstructions": "Audit .roo/.roomodes and docs; propose fixes; open GitHub issues if enabled. Always write a summary to docs/sessions/.",
  "groups": ["read","edit","browser","mcp","command"],
  "source": "project"
}
```

**New rules file `.roo/rules-common/apply-diff-discipline.md`:**

```md
# Apply-Diff Discipline
- Always re-read the target file immediately before creating a diff.
- Use exact search/replace blocks; include line numbers where the tool requires.
- Never use multiple `=======` separators within a single search/replace block.
- On mismatch, stop and re-read before attempting again.
```

**Logging rule stub `.roo/rules-common/logging.md`:**

```md
# Mode Logging
- Log start/end in NDJSON lines to `logs/aiGI/YYYY-MM-DD/{mode}.log.ndjson`.
- Include: ts, entrypoint, mode, task, action, status, artifacts, next.
- The orchestrator reads the last 50 lines before each major decision.
```

**System prompt anchor (search for “==== TOOL USE” in `roo_mode_defaults/system_prompt.md`) and replace with the revised block shown earlier.** ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))

**MCP expansion** (append into `.roo/mcp.json`): ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))

```json
"github": { "url": "https://mcp.composio.dev/github/<agent>", "alwaysAllow": ["repos_get","pulls_list","pulls_create","issues_list","issues_create"] },
"markitdown": { "url": "https://mcp.composio.dev/markitdown/<agent>", "alwaysAllow": ["fetch","convert"] },
"context7": { "url": "https://mcp.provider/context7/<agent>" },
"serena": { "url": "https://mcp.provider/serena/<agent>" }
```

**CLI command skeleton (add in `src/cli/commands/aigi-revised.js` and register in `src/cli/index.js`):** ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/src/cli/index.js?raw=1 "raw.githubusercontent.com"))

```js
const fs = require('fs-extra');
const path = require('path');
async function register(program) {
  program
    .command('aigi-revised')
    .description('Install or maintain aiGI Revised modes & rules')
    .command('setup')
      .description('Overlay .roomodes, .roo rules, MCP, and system prompt')
      .action(async () => { /* copy templates/aigi-revised/** to cwd with backups */ })
    .parent.command('doctor')
      .description('Validate installation (modes, rules, MCP endpoints)')
      .action(async () => { /* scan presence and basic json/md sanity */ });
}
module.exports = { aigiRevisedCommand: register };
```

---

## Documentation updates

* `README.md` (root): add a **“Selecting an Entrypoint”** section with your five workflows and a diagram of hand‑offs.
* `docs/vision/` (created by Visionary) with roadmap templates.
* `docs/sessions/README.md` describing session flow & how the Orchestrator resumes.
* `docs/memory/setup.md` – local DB + Mem0/Ollama options.

---

## Risks & mitigations

* **Prompt conflicts** (local overrides vs. Roo defaults). We explicitly override **by slug** in `.roomodes` and adjust `roo_mode_defaults/system_prompt.md`. (Roo respects project overrides.) ([GitHub](https://github.com/RooCodeInc/Roo-Code/issues/4277?utm_source=chatgpt.com "CODE mode lost default system prompt (&quot;You are Roo...&quot;) after ... - GitHub"))
* **MCP auth** differences across environments. Provide `.env.example` and a **doctor** command that checks endpoints.
* **Tool quotas** / rate limits. Orchestrator rules enforce  **backoff and fallback** .

---

## Why this is compatible with Roo

Roo’s **prompt structure** and **custom instructions** are designed for this type of project‑level override (rules directories, `.roomodes`, and prompt building). The plan leverages those extension points cleanly. ([Roo Code Docs](https://docs.roocode.com/advanced-usage/prompt-structure?utm_source=chatgpt.com "Prompt Structure | Roo Code Documentation"))

---

### Final notes on repository specifics I verified

* The repo is a **fork of rUv-dev** and already includes the **`create-sparc`** NPX and CLI, with templates and plans for SPARC scaffolding. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/tree/main/aiGI "aiGI_revised/aiGI at main · ThermoCannon/aiGI_revised · GitHub"))
* The **current system prompt** contains strict tool‑use constraints that we will replace. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/roo_mode_defaults/system_prompt.md?raw=1 "raw.githubusercontent.com"))
* The **current `.roomodes`** defines many modes but lacks an **`orchestrator` slug** override—so that’s a central change. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roomodes?raw=1 "raw.githubusercontent.com"))
* The **minimal Roo template doc** shows where to add rules/MCP/modes—exactly what we’ll leverage. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/templates/minimal-roo/.roo/README.md?raw=1 "raw.githubusercontent.com"))
* The **current MCP config** includes  **Supabase, Mem0, PerplexityAI** , which we’ll extend methodically. ([GitHub](https://github.com/ThermoCannon/aiGI_revised/blob/main/.roo/mcp.json?raw=1 "raw.githubusercontent.com"))

---

## Deliverables checklist for the implementing agent

* [ ] `.roomodes` updated with `orchestrator`, five entrypoints, and specialist modes.
* [ ] `roo_mode_defaults/system_prompt.md` revised (tool policy + dynamic context + fail‑safes).
* [ ] `.roo/rules-*` created with logging, apply‑diff, orchestrator, entrypoint, debugger, memory, indexing.
* [ ] `.roo/mcp.json` extended with GitHub/Markitdown/Context7/Serena (and gated in rules).
* [ ] New CLI command `aigi-revised setup|doctor|upgrade` implemented; templates under `templates/aigi-revised/**`.
* [ ] Session docs and logs created on first run; orchestrator reads and routes based on logs.
* [ ] Tests verifying the presence and basic function of the above.
* [ ] Documentation added for users to choose entrypoints and understand session continuity.

---

If you’d like, I can now generate the concrete patch files (diffs and new file contents) for the above so your Roo Code agent can apply them one‑shot.
