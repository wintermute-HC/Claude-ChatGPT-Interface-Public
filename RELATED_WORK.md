# Claude–Codex Collaboration: Prior Art and Related Work

English summary | [日本語（詳細な調査記録）](RELATED_WORK.ja.md)

Research date: **2026-11-15 JST**. This is a bounded literature and source review, not an exhaustive novelty search or a security certification. Issue and PR states below are as observed on that date.

## Conclusions

- The untracked-symlink context collection issue is already reported. This project's local marker test is an **independent replication**, not a claim of first discovery.
- Cross-model review, exchanging implementation/review roles, scoped delegation, and human approval also have public precedents.
- This repository contributes verification notes and proposed operating rules. It does not establish a novel general method or a measured improvement in safety.

## Directly related reports and fixes

| Source | Verified relationship and limits |
| --- | --- |
| [Official Issue #597](https://github.com/openai/codex-plugin-cc/issues/597) | Reported on 2026-08-05 against `db52e28`, including outside-repository symlink collection, reproduction, and mitigation. The issue author closed it as `not_planned`; that does not establish rejection by a maintainer. |
| [Official Issue #828](https://github.com/openai/codex-plugin-cc/issues/828) | Reports the same issue against 1.0.6 / `db52e28`; open when checked. |
| [Official PR #834](https://github.com/openai/codex-plugin-cc/pull/834/files) | Uses `lstatSync`, excludes symlinks and nonregular files, and adds regression tests. Open and unmerged when checked. Test success is the contributor's report; we did not rerun these tests. |
| [trevoraspencer fork PR #8](https://github.com/trevoraspencer/codex-plugin-cc/pull/8) | Merged on 2026-10-20 JST. Excludes links resolving outside the repository while retaining internal links. This is a fork fix, not an official merge. |
| [copilot-plugin-cc PR #148](https://github.com/tmathura/copilot-plugin-cc/pull/148) | Merged on 2026-10-27 JST. Related real-path containment and Windows junction issues in a different plugin. |

The official `main` still pointed to [`db52e28f4d9ded852ab3942cea316258ae4ef346`](https://github.com/openai/codex-plugin-cc/commit/db52e28f4d9ded852ab3942cea316258ae4ef346) when checked. This project has not patched the plugin.

Other permission-related sources were read in full: [Issue #75](https://github.com/openai/codex-plugin-cc/issues/75) reports a Claude deny-rule boundary; [Issue #673](https://github.com/openai/codex-plugin-cc/issues/673) concerns blocked outside-workspace writes and missing documentation. [PR #742](https://github.com/openai/codex-plugin-cc/pull/742) proposes sandbox selection; [PR #494](https://github.com/openai/codex-plugin-cc/pull/494) proposes automated approval review. Open proposals are not adopted specifications, and automated review is not human approval. These reports were not independently reproduced in this literature review.

## Workflow precedents

- [cross-agent-harness](https://github.com/harness17/cross-agent-harness): user-approved design, scoped Codex implementation, separate review, and a human commit decision.
- [claude-codex-loop](https://github.com/Whisker17/claude-codex-loop): independent design/code review with user design approval before implementation.
- [codex-claude-handoff](https://github.com/siglernir-ai/codex-claude-handoff): separate implementer/reviewer roles, role exchange, evidence records, and human approval for publishing actions.

These are precedents for approval-based collaboration, although their approval granularity differs from this repository's per-change rules. Their documentation was inspected; enforcement was not tested.

## Related experiments and research

- [MCP approval-policy experiment](https://zenn.dev/tyuya/articles/claude-codex-mcp-approval-gap): compares file creation under different approval policies. It uses `codex mcp-server` and different versions, so its observations cannot be directly transferred to this plugin's app-server path.
- [Cross-agent workflow article](https://zenn.dev/harness/articles/cross-agent-harness-automation), [codex-review](https://zenn.dev/shimo4228/articles/codex-review-cross-model-decorrelation), and [mutual review workflow](https://dev.to/iliyaoblakov/how-i-make-claude-code-and-codex-review-each-others-work-5dmh): prior operating approaches, including human decisions at different stages.
- [Du et al., 2023](https://arxiv.org/html/2305.14325v1): multiagent debate, including a small heterogeneous-model experiment; agreement can still be wrong. Relevant sections of the paper were read, but it does not evaluate this tool integration.
- [Irving et al., 2018](https://arxiv.org/abs/1805.00899): a human-adjudicated AI debate concept. Only the abstract and bibliographic information were checked in this review.

## Verification limits

We inspected public issue/PR bodies, important patches, workflow documentation, authored articles, and the academic material identified above. We did not install or execute the related projects, rerun their tests, or measure this operating proposal against alternatives. No claim of first discovery, comprehensive safety, or quantified error reduction follows from this review.

The [detailed Japanese record](RELATED_WORK.ja.md) includes additional sources, corrections to the earlier summary, and items that could not be verified.
