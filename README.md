# Claude-ChatGPT-Interface-Public

English | [日本語](README.ja.md)

Verification notes and proposed operating rules for using OpenAI Codex from Claude Code through [`openai/codex-plugin-cc`](https://github.com/openai/codex-plugin-cc).

This is an independent documentation and feedback project. The plugin itself is maintained in the OpenAI repository linked above. This repository records source inspections and limited tests conducted on **2026-11-15**. End-to-end installation and invocation from Claude Code, and a comprehensive security audit, have **not** been completed.

## Follow-up experiment

[2026-11-20 communication and verification tests](docs/2026-11-20-communication-test.md) covers a local shared-file relay, Codex display interruption and restart, and a synthetic cross-verification exercise. The November 15 plugin inspection and limited tests described below have a separate scope.

## Prior art and project positioning

This repository records **independent replications, verification notes, and proposed operating rules**. It does not claim first discovery of the symlink issue or invention of human-approved Claude–Codex collaboration. No comparative experiment has established that these rules improve safety or error rates.

The symlink issue was already described in [Issue #597](https://github.com/openai/codex-plugin-cc/issues/597) and [Issue #828](https://github.com/openai/codex-plugin-cc/issues/828); [PR #834](https://github.com/openai/codex-plugin-cc/pull/834) proposes a fix and remained unmerged when checked on 2026-11-15. Prior approval-based workflows include [cross-agent-harness](https://github.com/harness17/cross-agent-harness) and [codex-claude-handoff](https://github.com/siglernir-ai/codex-claude-handoff). Their approval boundaries differ from this project's rules.

See [Prior Art and Related Work](RELATED_WORK.md) and the [detailed Japanese research record](RELATED_WORK.ja.md) for sources, verification levels, and limitations.

## Ideas, feedback, and independent verification

We welcome ideas, corrections, improvement suggestions, and reports of independent tests through [Issues](https://github.com/wintermute-HC/Claude-ChatGPT-Interface-Public/issues). Please distinguish tests performed in **Claude desktop's Code environment** from CLI or lower-level script tests.

This repository accepts feedback rather than direct code contributions. Pull requests are disabled. Feedback does not grant write access; the owner decides which changes to adopt. The owner has limited time and cannot promise to reproduce every report or provide ongoing support.

For test reports, include the date, OS and application versions, plugin commit/version, steps, expected and actual results, and any changed files. Use synthetic data and redact credentials, personal information, and conversation history from logs. Do not test with unpublished research or sensitive data. Only run tests within environments and permissions you are authorized to use. If you cannot provide an appropriate test environment, documentation review and ideas are equally welcome.

### Sponsorship interest

Financial support for documentation and independent verification would be welcome. A payment channel has not yet been set up. If interested, please open an Issue expressing interest without posting payment details or other private information. Sponsorship does not grant repository write access, influence verification conclusions, or guarantee a particular result. Payment instructions will be published after a receiving account is established.

## Sources and scope

- Inspection and limited-test date: **2026-11-15**.
- Plugin snapshot: **1.0.6**, commit [`db52e28f4d9ded852ab3942cea316258ae4ef346`](https://github.com/openai/codex-plugin-cc/tree/db52e28f4d9ded852ab3942cea316258ae4ef346), dated 2026-07-07. Implementation statements below apply to this snapshot, not necessarily later versions.
- Sources whose contents were inspected: the [official README](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/README.md), [plugin manifest](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/.claude-plugin/plugin.json), source files linked below, and the [official pricing page](https://learn.chatgpt.com/docs/pricing), reached through the former `developers.openai.com/codex/pricing` URL.

Project-specific rules are in [CLAUDE.md](CLAUDE.md). They are instructions to agents, not technical enforcement of permissions.

## What the official documentation says

| Topic | Documented behavior or condition |
| --- | --- |
| Publisher | The plugin is published under GitHub's `openai` organization. Its manifest names OpenAI as author; its [license is Apache-2.0](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/LICENSE). This does not guarantee runtime safety. |
| Installation | Add the marketplace, install the plugin, run `/reload-plugins`, then `/codex:setup`. Commands are reproduced below. |
| Authentication | ChatGPT sign-in or an OpenAI API key. The plugin README mentions Free, whereas the pricing page inspected describes Free access in the desktop app. We have not established that a Free account can use this CLI-based plugin. |
| Delegation | The README documents `/codex:rescue` and delegation requests in natural language. Roles and approval requirements for this project are defined in `CLAUDE.md`. |
| Integration | The plugin uses the local Codex CLI, its authentication state, and Codex app-server on the same machine. Local integration does not mean that no information reaches external services. |
| Hooks | The [hook configuration](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/hooks/hooks.json) declares `SessionStart`, `SessionEnd`, and `Stop`. The review gate is disabled in the [default state](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/lib/state.mjs). |
| Installing Codex | The [setup command instructions](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/commands/setup.md) tell Claude to ask before installing Codex when it is missing and npm is available. An instruction is distinct from an OS-enforced restriction. |

### Static inspection coverage

Inspection covered relevant parts of the README, manifest, dependency declarations, delegation instructions, approval and sandbox settings, session import, hooks, review gate, setup instructions, review-context collection, state files, and local communication. This was not an audit of every source file, dependency, or runtime component. These observations must be distinguished from the tests below.

### Usage and billing

- **ChatGPT sign-in:** Codex use is subject to the account's plan and applicable limits. Free-account eligibility for this plugin remains unverified.
- **API-key authentication:** API usage is billed under API pricing, separately from a ChatGPT subscription's included usage.
- **Current limits:** Check [official pricing](https://learn.chatgpt.com/docs/pricing) and the [usage dashboard](https://chatgpt.com/codex/settings/usage). This document does not fix plan prices or task counts, because conditions can change.

## Tests performed and their limits

Environment: macOS, Node.js **v26.13.1**, npm **11.20.0**, Codex CLI **0.169.0**, and the plugin snapshot identified above. Tests used dedicated synthetic repositories and harmless text, not research data or conversation histories.

| Test | Result | What this establishes |
| --- | --- | --- |
| Official automated tests: `npm test` | **91 passed, 0 failed, 0 skipped** | Includes tests using a simulated Codex. This does not demonstrate that every live-service feature works. |
| Lower-level plugin script connecting to actual Codex | Passed | `setup` returned `ready: true`; ChatGPT authentication was confirmed. |
| Synthetic task sent to actual Codex | Passed | The requested marker was returned; the response's changed-file list was empty. No file operations were requested and no write option was supplied. |
| Normal review using actual Codex | Passed | Detected a synthetic change from addition to subtraction. The reviewed file remained unchanged afterward. |
| Collection of text outside a workspace through an untracked symlink | **Reproduced locally** | A harmless external marker appeared in collected context. Sending that marker to the service was not tested. |

The automated tests were run from the [official test suite](https://github.com/openai/codex-plugin-cc/tree/db52e28f4d9ded852ab3942cea316258ae4ef346/tests). Live-service tests invoked `setup`, `task` without write permission, and `review` through `codex-companion.mjs`. They were **not** invoked through Claude Code's slash commands or its desktop Code interface.

An initial setup attempt could not open Codex's state database under the test runner's access restrictions; it succeeded after the required permission was granted. The initial restricted automated-test run was stopped and rerun with permission. The table reports the completed rerun.

### Independently reproduced known review-context boundary issue

This is a replication of the known issue reported in [#597](https://github.com/openai/codex-plugin-cc/issues/597) and [#828](https://github.com/openai/codex-plugin-cc/issues/828), not a new discovery. Link-exclusion mitigation is also proposed in [PR #834](https://github.com/openai/codex-plugin-cc/pull/834).

In the inspected version, context collection for `/codex:adversarial-review` can follow an untracked symbolic link and include text from outside the working repository. A local invocation of `collectReviewContext`, using a harmless marker outside a synthetic repository, reproduced this behavior.

The implementation incorporates this context into the adversarial-review prompt. No existing user files or secrets were used, and actual service transmission of the marker was not tested. This observation alone does not establish remote exploitability.

Before reviewing, inspect untracked files and their link targets. Do not include links to information outside the approved sharing scope. A code-level mitigation would skip such links or validate that their resolved targets remain inside the workspace. **The plugin itself has not been patched in this project.**

Sources: [untracked-file collection](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/lib/git.mjs#L197), [insertion into the review prompt](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/codex-companion.mjs#L409).

### Not yet verified

- End-to-end installation and invocation in Claude Code, including Claude desktop's Code interface. The shell-based test environment did not locate a standalone Claude Code executable; that does not establish whether desktop Code is available. Later screen access to desktop Code does not constitute an end-to-end plugin test.
- Enforcement of write boundaries with a live model, transfer of real conversation history, and a live review-gate interaction with Claude Code.
- Captured network traffic and destination verification; Codex/Claude internals; dependency implementations and distributed packages; Windows behavior.
- Comprehensive tests of state-file tampering, local IPC permissions and input sizes, and concurrent operations.
- Live-service use with API-key authentication, eligibility of individual account types including Free, and actual billing amounts.

These results are not a comprehensive security audit or a guarantee of all features or overall safety.

## Installation steps from the official README

These steps are documented procedures, not a claim that desktop installation was tested here. Run them in Claude Code on a machine with access to your chosen repository. Cloud availability depends on that environment's configuration and permissions.

Requirements: Node.js **18.18 or later**, and a ChatGPT account eligible for Codex CLI or an OpenAI API key. Confirm account eligibility using current official information.

1. Add the marketplace:

   ```text
   /plugin marketplace add openai/codex-plugin-cc
   ```

2. Install the plugin:

   ```text
   /plugin install codex@openai-codex
   ```

3. Reload plugins:

   ```text
   /reload-plugins
   ```

4. Check setup:

   ```text
   /codex:setup
   ```

   The setup instructions call for asking before installing Codex if it is missing and npm is available. The documented manual installation command is `npm install -g @openai/codex`.

5. If not signed in, run:

   ```text
   !codex login
   ```

6. Check that `codex:codex-rescue` appears under `/agents`. This alone does not verify authentication or successful execution. For an initial read-only review, the official README suggests:

   ```text
   /codex:review --background
   ```

   Check progress with `/codex:status`, then retrieve the completed result with `/codex:result`. A launch message alone is not proof of success; there may be no reviewable changes or an execution error.

## Documented commands

| Command | Purpose |
| --- | --- |
| `/codex:review` | Read-only review of uncommitted changes, or a branch comparison using `--base <ref>`. |
| `/codex:adversarial-review` | Read-only challenge review of assumptions and design choices; accepts focus text. Does not fix code. |
| `/codex:rescue` | Delegate investigation or implementation tasks to Codex. Check write permissions first. |
| `/codex:transfer` | Create a Codex thread from the current Claude session and print `codex resume <session-id>`. |
| `/codex:status` | Show running and recent jobs. |
| `/codex:result` | Retrieve a completed job's result. |
| `/codex:cancel` | Cancel an active background job. |
| `/codex:setup` | Check installation and authentication; optionally enable or disable the review gate. |

## Project roles and approval

Claude and Codex may both analyze, reason, implement, and verify. Roles are not fixed. Important decisions should receive independent cross-checking where practical, with final authority remaining with the user.

- Do not change existing files during investigation or review.
- Before writing, present the target files, rationale, diff, expected impact, destination, and executing AI; obtain explicit approval.
- Prefer a new filename. Overwriting requires explicit approval identifying the target.
- Do not change files through automatic delegation. Restrict changes to the scope and executor approved by the user.
- File changes and Git commit/push require separate approvals.
- A command invocation is not a substitute for approval of changes.
- Use `/codex:transfer` only with explicit approval, and enable the review gate only when explicitly requested.

See [CLAUDE.md](CLAUDE.md) for the complete rules. These instructions do not replace OS or sandbox controls.

## Operational cautions

- **Delegation and writing:** The inspected [rescue-agent instructions](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/agents/codex-rescue.md) encourage proactive delegation. They also direct the agent to add `--write` by default, except for explicitly read-only requests or review, diagnosis, or research without edits. The [task implementation](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/codex-companion.mjs) chooses `workspace-write` with `--write`, otherwise `read-only`; the [approval-policy default](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/lib/codex.mjs) is `never`. Additional confirmation is not assured for edits within the allowed scope. This project requires approval before changes. Explicitly request read-only investigation and verify actual permissions; stop change operations if approval requirements cannot be enforced, as required by section 7 of `CLAUDE.md`.
- **Read-only does not mean no data collection:** Not editing target code is different from limiting collected information or service transmission. See the symlink issue above. The plugin also writes state, job, and log files and has cleanup and process-termination logic; read-only review does not mean that no state files are written.
- **Conversation transfer:** `/codex:transfer` passes a selected session under `~/.claude/projects` to the local app-server's session importer. The plugin's import request was inspected; Codex's internal import behavior and network traffic were not measured. Subsequent use may transmit information to the service. Check the history and sharing scope and obtain explicit approval first.
- **Usage:** ChatGPT sign-in follows applicable Codex plan conditions; API-key use follows API pricing. Consult current pricing and the usage dashboard.
- **Review gate:** `/codex:setup --enable-review-gate` enables review through the `Stop` hook. The [implementation](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/stop-review-gate-hook.mjs) can block stopping for findings, review failures, timeouts, or invalid output. Some setup conditions instead return without reviewing. It does not guarantee successful fixes. Long loops can consume usage quickly; enable only on explicit request and while monitoring. Disable with `/codex:setup --disable-review-gate`.
- **Configuration:** The plugin uses the same local Codex installation and authentication state, and reads its configuration. Individual operations also explicitly set some approval and sandbox values. Project `.codex/config.toml` files load only for trusted projects. A `CLAUDE.md` file alone cannot guarantee technical enforcement.
