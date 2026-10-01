# Synapse — an internal AI knowledge platform (case study)

> Product case study. The source code is company IP and stays private; this write-up covers the problem, the architecture, and the product decisions. I was the product manager and hands-on builder.

## The problem

A ~300-person services company had its operating knowledge scattered across Notion, Slack threads, spreadsheets, and people's heads. New joiners took weeks to find basic process answers; department leads answered the same questions repeatedly; and sensitive material (finance, HR) could not go into any shared search tool without access control.

## The product

Synapse is a desktop knowledge platform with an AI answer layer:

- **Ask, don't browse.** Employees ask questions in natural language; answers are generated over a retrieval layer (RAG) so they cite real company documents rather than improvising.
- **Department-scoped access as a first-class concept.** Content is segmented across a department taxonomy (7 departments at launch) with an admin/department/agent hierarchy — finance content is simply not retrievable outside finance. Access control lives in the retrieval layer, not in a prompt.
- **Zero-friction distribution.** Ships as a desktop app with silent in-app auto-update (static update feed on object storage behind a CDN), so version rollout needs no IT involvement.
- **Login the way the company already works** — mobile-number OTP against the existing employee directory, no new accounts to manage.

## Architecture (high level)

```
Electron desktop app
  -> auth service (OTP against existing employee directory)
  -> retrieval layer: department-scoped RAG over synced company docs
  -> LLM answer generation, grounded in retrieved chunks only
Auto-update: signed builds on object storage + CDN, in-app updater polls a version feed
```

## Product decisions that mattered

1. **Access hierarchy before AI quality.** The blocker to adoption wasn't answer quality — it was trust that the tool would not leak cross-department content. Segmentation was built first and demonstrated to leadership before the answer layer was tuned.
2. **Desktop over web.** The audience included field-operations staff on shared machines; a desktop app with OS-level presence, minimise-to-tray, and offline tolerance beat another browser tab.
3. **Auto-update as a product feature.** Internal tools die when updating requires IT. The in-app updater turned weekly iteration into something users never noticed.
4. **Grounded answers only.** The answer layer refuses to go beyond retrieved documents. "I don't have that" preserves trust better than a fluent guess.

## Outcome

- Live in production, adopted across 7 departments (~290 registered users).
- Shipped from concept to v0.4.x as a working desktop platform, iterating weekly via the auto-update pipeline.

## My role

Product manager and primary builder: concept, access-model design, architecture decisions with engineering, rollout, and iteration. Related public work: [pm-agents](https://github.com/saumya-dabi/pm-agents) (agentic PM tooling) and [vault-rag](https://github.com/saumya-dabi/vault-rag) (the local-first RAG pattern Synapse's retrieval layer grew out of).
