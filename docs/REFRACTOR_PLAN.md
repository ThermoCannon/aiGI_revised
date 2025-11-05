# Refractor Execution Plan — aiGI_revised

References:
- Source intent and requirements: [`project_prompt1.md`](project_prompt1.md:1)
- Draft orchestration plan used as source: [`draft_plan1.md`](draft_plan1.md:1)

Purpose
1. Provide a single, implementation-ready refactor plan that an autonomous agent can follow to transform the repository into the entrypoint‑centric aiGI system described in the sources.
2. Preserve scope boundaries, clear phase breakdown (A–G), granular task decomposition, templates for autonomous execution, QA and rollback guidance, and a PR roadmap.

Executive summary
- Scope boundaries: refactor repository mode definitions, system prompt, .roo rules, MCP config, logging/session systems, CLI installer extensions, and new specialist modes. Do not modify production application logic outside scaffolding and mode/rules configuration unless required by the listed tasks.
- Key assumptions:
  1. The repo root contains the files referenced above and templates shown in the draft. See [`project_prompt1.md`](project_prompt1.md:1) and [`draft_plan1.md`](draft_plan1.md:1).
  2. Implementer has write access and can create templates under `templates/`.
  3. CI runs via package.json (Jest). Existing tests are a baseline.
  4. Secrets will not be committed; `.env.example` will be used.
- Deliverable: docs/REFRACTOR_PLAN.md (this file), plus a sequence of PRs (PR 0–PR 9) with patches, tests, and templates.

Table of contents
1. Strategic Phase Planning (Phases A–G)
2. Granular Task Decomposition (per phase)
3. Autonomous Execution Template (per-task)
4. Quality Assurance Framework
5. Risk Register & Recovery
6. Progress Tracking & Logging
7. Resource Allocation & Timeline
8. Contingency & Rollback Procedures
9. Final Integration & Deployment
10. Appendices (templates, CLI skeletons, PR roadmap)
11. Next actionable step

1. Strategic Phase Planning (Phases A–G)
Phase A — Preparation & Repository Hygiene
- Goal: create baseline docs and repo hygiene artifacts.
- Entry criteria: repo clone available, tests run (npm test passes baseline), package.json readable.
- Exit criteria: CONTRIBUTING.md, SECURITY.md, CHANGELOG.md, docs/ARCHITECTURE.md added; small sanity tests added.
- Dependencies: none.
- Rollback: remove added doc files or revert PR.

Phase B — Entrypoint Orchestration & Mode Definitions
- Goal: implement `orchestrator` override and five entrypoint mode definitions in `.roomodes` and add specialist mode entries.
- Entry criteria: Phase A complete.
- Exit criteria: `.roomodes` contains orchestrator + entrypoint and specialist modes; basic rule stubs present in `.roo/`.
- Dependencies: Phase A.
- Rollback: revert `.roomodes` to previous version (backup copy saved by installer).

Phase C — System Prompt Rework & Rules
- Goal: replace strict tool-use constraints in `roo_mode_defaults/system_prompt.md`, add dynamic workspace context guidance, and add `.roo/rules-*` files.
- Entry criteria: Phase B complete.
- Exit criteria: new system_prompt.md in place, rules files present under `.roo/`.
- Dependencies: Phase B.
- Rollback: restore previous `roo_mode_defaults/system_prompt.md` from backup.

Phase D — Logging, Session Management, Memory & Debugger
- Goal: implement NDJSON logging schema, session-start/stop mode, improved debugger loop, and memory-manager rules & scripts.
- Entry criteria: Phase C complete.
- Exit criteria: logs/aiGI template in repo, session doc templates under docs/sessions/, rules-debugger and rules-memory present, helper scripts under scripts/.
- Dependencies: Phase C.
- Rollback: revert created log templates and scripts; clear any created log files.

Phase E — MCP Expansion & Gating
- Goal: extend `.roo/mcp.json` with GitHub, Markitdown, Context7, Serena entries and gate their use via rules.
- Entry criteria: Phase D complete.
- Exit criteria: `.roo/mcp.json` extended, gating rules present, and sample tokens excluded (use `.env.example`).
- Dependencies: Phase D.
- Rollback: restore previous `.roo/mcp.json` and remove gating rules.

Phase F — CLI Single Installer & Templates
- Goal: add `aigi-revised` installer commands (setup, doctor, upgrade) and templates under templates/aigi-revised/.
- Entry criteria: Phase E complete.
- Exit criteria: CLI commands implemented in src/cli, templates present, installer backup behavior implemented.
- Dependencies: Phases A–E.
- Rollback: remove command registration or revert src/cli changes.

Phase G — Hardening, Tests, Codebase-Analysis Mode
- Goal: add codebase-analysis mode, tests for presence/sanity, CI integration and final docs.
- Entry criteria: Phase F complete.
- Exit criteria: tests pass in CI, docs updated, codebase-analysis outputs templates present.
- Dependencies: Phases A–F.
- Rollback: revert test additions and codebase-analysis mode changes.

2. Granular Task Decomposition (per phase)
Phase A tasks
A.1 Create CONTRIBUTING.md, SECURITY.md, CHANGELOG.md
- Inputs: repo README.md, plans/
- Outputs: files at repo root
- Acceptance criteria: files exist and contain minimal guidance; tests that check for file existence succeed
- Estimated effort: 1–2 hours
- Validation checkpoint: CI step detects files

A.2 Add docs/ARCHITECTURE.md (link to plans/)
- Inputs: plans/ folder
- Outputs: docs/ARCHITECTURE.md
- Acceptance criteria: document links to key plans and explains entrypoint concept
- Effort: 1 hour

Phase B tasks
B.1 Extend `.roomodes` with orchestrator + entrypoint and specialist modes
- Inputs: current `.roomodes` file
- Outputs: updated `.roomodes`
- Acceptance criteria: JSON validates; orchestrator slug equals "orchestrator"
- Effort: 2–4 hours
- Validation: unit test that parses `.roomodes` and checks slugs

B.2 Create `.roo/rules-orchestrator/core.md` and entrypoint rule stubs
- Inputs: draft plan guidance
- Outputs: rules files
- Acceptance criteria: rule files present and referenced in docs

Phase C tasks
C.1 Update `roo_mode_defaults/system_prompt.md` (Tool Use policy + Dynamic Context)
- Inputs: current system_prompt.md
- Outputs: revised prompt
- Acceptance criteria: prompt no longer mandates "one tool per message"
- Effort: 1–2 hours
- Validation: static check that forbidden phrase removed

C.2 Add `.roo/rules-common/apply-diff-discipline.md` and logging.md
- Inputs: draft templates
- Outputs: rules files
- Acceptance criteria: files present and formatted

Phase D tasks
D.1 Add log schema and logger template (logs/aiGI/README + logger example)
- Inputs: logging spec
- Outputs: .md + sample NDJSON schema
- Acceptance criteria: examples exist and unit test can validate NDJSON lines
- Effort: 2 hours

D.2 Implement session-startstop mode docs and templates (docs/sessions/)
- Inputs: orchestrator rules
- Outputs: session templates
- Acceptance: files exist and orchestrator references them

D.3 Provide rules-debugger/improved-diagnostics.md and helper scripts/scripts/env
- Inputs: draft plan
- Output: rules + scripts
- Acceptance: script is non-executable but present and documented

D.4 Memory manager instructions (rules-memory/manager.md + scripts/memory/)
- Inputs: draft plan
- Output: rules + scripts
- Acceptance: present and documented

Phase E tasks
E.1 Update `.roo/mcp.json` with new server entries (safe placeholders)
- Inputs: existing .roo/mcp.json
- Output: extended .roo/mcp.json (no secrets)
- Acceptance: JSON validates and gating rules reference entries

E.2 Update entrypoint rule files to restrict MCP usage
- Acceptance: rule files indicate allowed MCPs

Phase F tasks
F.1 Add CLI command skeleton under src/cli/commands/aigi-revised.js and register in src/cli/index.js
- Inputs: existing CLI framework
- Output: command skeleton
- Acceptance: `node bin/index.js help` shows aigi-revised command (basic)

F.2 Add templates/aigi-revised/ with .roo and .roomodes stubs and system_prompt override
- Acceptance: templates present and `setup` copies them with backups

Phase G tasks
G.1 Add codebase-analysis mode (rules + docs/codebase/)
- Inputs: list_files and read_file usage plan
- Output: docs/codebase/summary.md and deps.mmd templates
- Acceptance: run simulation script produces the files

G.2 Tests & CI
- Add Jest tests that:
  - verify `.roomodes` contains required slugs
  - verify `.roo/rules-*` presence
  - validate basic prompt text replaced
- Acceptance: npm test passes in CI

3. Per-task autonomous execution template (single unified template; apply to each task)
- Pre-checks:
  1. Confirm file(s) exist with read_file.
  2. Back up originals to `backup/<timestamp>/...` (copy files; do not commit secrets).
  3. Validate JSON/MD syntax locally (jsonlint/mdlint).
- Action steps:
  1. Execute pre-checks.
  2. Apply changes via exact, anchor-based edits; for new files create under templates/aigi-revised or .roo/rules-*.
  3. Run linters / unit tests for the modified area.
  4. Write NDJSON log line indicating status.
  5. Create PR branch: `pr/<phase>-<task>-<short>`.
- Error handling:
  - If syntax error found, revert to backup and log error with "status":"failed", "error":"...".
  - If tests fail, run "diagnose" routine: collect logs, list failing tests, attempt auto-fix if trivial, else mark as needs-review.
- Quality gates:
  - JSON must lint clean.
  - No secrets in file content.
  - Tests for the task must pass.
- Decision tree example (simplified):
  1. Pre-check pass? Yes -> proceed. No -> backup & abort.
  2. Apply change -> Lint pass? Yes -> run tests. No -> revert & record failure.
  3. Tests pass? Yes -> commit & open PR. No -> attempt auto-fix (if small) or mark for human review.
- Logging: write NDJSON with fields ts, entrypoint, mode, task, action, summary, artifacts, status, next.

4. Quality Assurance Framework
- Tests:
  1. Unit tests (Jest) for parsing config files and rules.
  2. Integration tests: installer `setup` copies templates and `doctor` validates installation.
  3. End-to-end smoke: `npx create-sparc aigi-revised setup` in a temp dir and run `doctor`.
- CI:
  - Add pipeline steps:
    1. Checkout
    2. Node install
    3. Lint (eslint + markdownlint)
    4. Run Jest tests
    5. Static checks: JSON validation, no secret patterns
- Metrics:
  - Test coverage goal: >= 80% for new code and 60% repo-wide as interim.
  - Lint zero-tolerance on errors (warnings allowed).
  - Logging coverage: 100% of orchestrator and entrypoint mode runs must emit NDJSON summary lines.
- Lint rules:
  - Maintain existing ESLint/Prettier configs.
  - Add markdownlint rules for docs.
  - Add JSON schema validation for `.roomodes` entries (required fields: slug, name, roleDefinition).
- Security checks:
  - Prevent secrets in repo: search for `PRIVATE_KEY|AWS_SECRET|TOKEN|PASSWORD` in changes.
  - `.env.example` only; instruct users to populate real secrets in CI runtime variables.

5. Risk Register with mitigation & recovery procedures
- Risk: Prompt conflicts with Roo defaults
  - Mitigation: override by slug; add explicit instruction comments.
  - Recovery: revert prompt file; re-run tests.
- Risk: Accidental secret commit
  - Mitigation: pre‑commit hook (recommend) and CI scan; `.gitignore` entries.
  - Recovery: rotate secrets, remove via git filter-branch and notify stakeholders.
- Risk: MCP tokens missing or invalid
  - Mitigation: use `.env.example`; `doctor` validates endpoints and reports required env variables.
  - Recovery: disable MCP-dependent features in orchestrator and continue with degraded mode.
- Risk: Stalls due to tool failures
  - Mitigation: updated prompt mandates "propose next action and proceed"; orchestrator monitors logs and restarts flows.
  - Recovery: orchestrator returns task to session-startstop and flags for human review.
- Risk: CI breaks after changes
  - Mitigation: write focused tests for changed modules; keep PRs small.
  - Recovery: revert PR or apply patch from backup branch.

6. Progress Tracking methodology
- Milestones:
  - M1: PR 0 merged (repo hygiene)
  - M2: PR 1 merged (.roomodes + rules stubs)
  - M3: PR 2 merged (system prompt)
  - M4: PR 3–5 merged (logging, debugger, MCP)
  - M5: PR 6 merged (CLI installer)
  - M6: PR 7–9 merged (analysis, tests, hardening)
- Logging schema: NDJSON per line (example from draft):
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
- Reporting:
  - Weekly status files: docs/reports/status-YYYY-MM-DD.md
  - CI badges in README updated on PR merges
- Feedback loops:
  - Orchestrator reads latest 50 lines from logs before deciding next action.
  - Each PR includes a "migration note" mapping changed files and required manual steps.

7. Resource allocation & timeline estimates
- Assumptions: single full-time engineer or equivalent autonomous agent with CI access.
- Estimated timeline (calendar):
  - Phase A: 1–2 days
  - Phase B: 2–3 days
  - Phase C: 1–2 days
  - Phase D: 3–5 days
  - Phase E: 1–2 days
  - Phase F: 2–3 days
  - Phase G: 2–4 days
- Total: 12–21 working days
- Resource allocation:
  - Owner: Lead agent (or human maintainer)
  - Reviewers: 1 security reviewer, 1 QA, 1 documentation reviewer
  - CI maintainer for pipeline steps

8. Contingency & Rollback instructions
- General rules:
  1. Always create backups: copy modified files to backup/<timestamp>/path.
  2. Create PR branch with descriptive name and link to tasks.
- CLI rollback examples (to be run in repo root):
  - Restore backup copy (example):
    - cp -r backup/2025-11-05T160000/.roomodes .roomodes
  - Revert a commit and force push (human-reviewed):
    - git checkout main && git pull origin main
    - git revert <commit-sha> --no-edit
    - git push origin main
- Backup locations:
  - backup/<timestamp>/ (store original files)
  - optional: artifacts/PR-<n>/ for patch bundles

9. Final Integration & Deployment
- Pre-release checklist:
  1. All PRs merged for Phases A–G.
  2. CI green.
  3. README updated with "Selecting an Entrypoint" section.
  4. docs/sessions/ and logs/ templates present.
- Deployment steps:
  1. Merge all PRs to main.
  2. Tag release v1.0-aiGI-revised.
  3. Publish package if applicable (npm publish) — only if owner confirms.
- Post-release monitoring:
  - Monitor CI runs and error rates for 72 hours.
  - Orchestrator should write a post-release session summary to docs/sessions/.
  - Collect bug reports and triage into next milestone.

10. Appendices

A. Example files to create (paths relative to repo root)
- `.roo/rules-common/logging.md`
- `.roo/rules-common/apply-diff-discipline.md`
- `.roo/rules-orchestrator/core.md`
- `.roo/rules-orchestrator/entry-data-exploration.md` (and each entrypoint)
- `templates/aigi-revised/.roomodes`
- `templates/aigi-revised/roo_mode_defaults/system_prompt.md`
- `templates/aigi-revised/.roo/*` (all rules)
- `src/cli/commands/aigi-revised.js`
- `scripts/env/setup_python.sh` (non-executable template)
- `scripts/memory/setup_ollama.md` (instructions)
- `docs/sessions/README.md`
- `docs/codebase/summary.md`
- `docs/memory/setup.md`
- `backup/` (created by installer during setup)

B. CLI command skeleton (location: `src/cli/commands/aigi-revised.js`)
- See `src/cli/index.js` for registration. Implement actions:
  - setup: copy templates with backups
  - doctor: validate presence of required files and env vars
  - upgrade: reapply templates preserving local edits with .bak

C. NDJSON log schema example
- See section 6. Use this exact field set: ts, entrypoint, mode, task, action, summary, artifacts, status, next.

D. PR roadmap (PR 0–PR 9)
- PR 0: Repository hygiene (CONTRIBUTING, SECURITY, CHANGELOG, docs/ARCHITECTURE)
- PR 1: Entrypoint orchestration & `.roomodes` + `.roo/rules-*` stubs
- PR 2: System prompt overhaul
- PR 3: Logging & session-startstop
- PR 4: Debugger & memory manager scripts and rules
- PR 5: MCP expansion & gating
- PR 6: CLI `aigi-revised` installer & templates
- PR 7: Codebase analysis mode + docs/codebase
- PR 8: Multi-session continuity enforcement and session docs auto-generation
- PR 9: Hardening, tests, CI enhancements, final docs

11. Next actionable step
- Generate concrete patch files for PR 0 and PR 1:
  1. Create PR 0 patches: add CONTRIBUTING.md, SECURITY.md, CHANGELOG.md, docs/ARCHITECTURE.md, small CI checks.
  2. Create PR 1 patches: update `.roomodes` with orchestrator and entrypoint templates and add `.roo/rules-orchestrator/*` stubs.
- Recommendation: run these two PRs first to establish the scaffolding and governance before deeper edits.

End of plan.