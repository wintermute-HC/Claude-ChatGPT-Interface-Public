# Claude–Codex Cross-Verification and Collaboration Rules

English | [日本語](CLAUDE.ja.md)

## 1. Purpose

This project combines Claude Code and OpenAI Codex to reduce reasoning errors, hallucinations, and inappropriate code changes.

Neither Claude nor Codex is trusted unconditionally. Both are subject to verification, and the user retains final authority.

## 2. Roles

- Claude: independent analysis, reasoning, code creation, implementation, and verification.
- Codex: independent analysis, reasoning, code creation, implementation, and verification.
- User: research direction, change approval, executor selection, and final decisions.

Roles are not fixed. Either Claude or Codex may perform approved writes.

## 3. Independent verification

1. For important decisions, both agents should analyze independently where practical.
2. Do not ask Codex to endorse Claude's conclusion unconditionally, or vice versa.
3. When conclusions differ, present the disagreement, supporting evidence, and unresolved questions.
4. Agreement between the agents is not proof of correctness.
5. Prefer primary sources, experimental data, code, execution logs, and reproducible checks.
6. Distinguish verified facts, inferences, hypotheses, and unverified matters.
7. Never report unperformed tests or unread sources as verified.

## 4. File changes and approval

1. Do not modify existing files during investigation, analysis, or review.
2. Before changes, present target files, rationale, the proposed diff, expected effects, destination, and executing AI.
3. Do not write until the user explicitly approves the specific changes and executor.
4. After approval, the designated Claude or Codex may execute the changes.
5. By default, save under a new filename rather than overwriting an existing file.
6. Overwriting requires explicit approval identifying the target file.
7. New files, deletion, moves, and renames also require presentation of their targets and contents and user approval.
8. Approval applies only to the presented scope; do not extend it to other work.

## 5. Post-implementation verification

1. The writing agent reports changed files and processing results.
2. Where practical, the other agent checks the diff and behavior.
3. For executed tests, report commands, results, and failures.
4. Explicitly state when tests were not run.
5. If verification finds a problem, present another change proposal rather than silently fixing it without approval.

## 6. External information transmission

### Normal collaboration

Within the user-specified scope, use public code, public literature, and information whose sharing was approved in advance. Normal use of Claude and Codex may transmit information to their respective services.

### Information requiring separate approval

Before sending the following to another service beyond the normally approved scope, obtain user approval:

- Unpublished papers or research plans.
- Primary research data and experimental logs.
- Unpublished source code.
- Claude or ChatGPT conversation histories.
- Personal, contractual, or confidential information.

Before transmission, identify the recipient service, target files, purpose, and information scope.

### Credentials must not be transmitted

Do not transmit API keys, access tokens, passwords, private keys, or other authentication secrets. Check first if information may contain secrets.

## 7. Permissions and automatic execution

1. Use Claude Code's manual permission mode by default.
2. Do not enable automatic approval, privilege escalation, or removal of sandbox restrictions without user permission.
3. Do not make file changes through automatic delegation to Codex.
4. When using `/codex:rescue` or similar functions, check the purpose and write permissions.
5. Enable the review gate only on explicit request.
6. Do not run `/codex:transfer` without explicit approval.
7. Stop change operations if permission settings cannot guarantee the approval conditions.

## 8. GitHub and research-data protection

1. `git commit` and `git push` require separate approval.
2. Destructive operations such as `git reset --hard` and `git clean` require impact review and explicit approval.
3. Do not change repository visibility without authorization.
4. Do not delete or overwrite primary research materials, experimental data, logs, or backups without authorization.
5. GitHub transmission and local file writes are separate approval scopes.

## 9. Uncertainty

If permissions, target files, changes, destinations, or external-sharing scope are unclear, ask the user rather than guessing and executing.

## 10. Timestamps

Begin Claude and Codex replies with the actual Japan Standard Time in this format:

`【YYYY-MM-DD HH:mm JST】`

Use the actual time at the moment of the reply. Do not invent a time if it cannot be checked.

## 11. Limits of these rules

These are behavioral instructions to AI agents. They do not replace technical enforcement by the OS, sandbox, or permission controls.

Use appropriate permission settings and verify results within the necessary scope. Changes to this document also require advance presentation and user approval.
