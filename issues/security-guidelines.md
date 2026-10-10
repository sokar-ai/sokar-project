# Security guidelines – how Sokar measures up

Twenty security guidelines for AI coding tools and AI systems, compared point by point with what Sokar
does. Every gap that is to be closed is an issue in the repository that builds it, linked from the
tables below.

## Summary

- **Over all 73 points:** ✅ 33 · 🟡 planned 31 · ❌ 8 · ⚪ 1
- **Met:** human review (the gate, risky files first; on the forge in `online`), no provider or Git
  credentials in the container, short-lived tokens per task, default-deny egress, rootless containers,
  fail-closed, isolated environments, signed and filtered messages between tasks, encryption, data
  minimisation, immediate revocation, updates through the package manager.
- **Scope:** which model and provider is used lies outside Sokar. Whether data may go to an external
  vendor (e.g. the City of Zurich's AI directive Art. 6) is the user's to secure outside Sokar; Sokar
  works with any provider, including one of the user's own. Supporting features (a local-provider
  template, allowed providers per project, `sokar inventory`) help the user meet those obligations.
- **Not met, deliberately:** detecting prompt injection and encoded instructions (Sokar limits the
  damage instead); avoiding the lethal trifecta completely (the channel to the provider stays open,
  other ways are limited, alerting catches anomalies); AI-assisted review (fixed rules rather than
  another model); rotating the vault's master key (the stored keys are rotated by the user at each
  vendor); MFA inside Sokar (the operating system's and ssh's; for weighty approvals `gate approve
  --signed` with a hardware key); separating untrusted data from tool calls (the agent's); roles/RBAC
  (the operating system's and the organisation's; four eyes at the gate is planned).

## Comparison by guideline

**Legend "Met?":** ✅ met · 🟡 planned – an issue would meet the point · ❌ not met, deliberately ·
⚪ not relevant · "see also" – an issue covers part of it

A point named by several guidelines appears under each. "Where in the guideline" gives item, section,
chapter, article or requirement in that guideline's own numbering; the source is linked under each
heading. File references in "Evidence" and "Remark" are to Sokar's documentation.

### Overview

| Guideline | Points | ✅ | 🟡 planned | ❌ | ⚪ |
|---|---|---|---|---|---|
| [Acalvio](#acalvio) | 8 | 1 | 7 | 0 | 0 |
| [ACSC et al. – Engaging with Artificial Intelligence (January 2024)](#acsc) | 7 | 3 | 2 | 1 | 1 |
| [BSI – AI transparency checklist](#bsit) | 4 | 3 | 1 | 0 | 0 |
| [BSI – Criteria catalogue for AI in the federal administration (v1.0, 2025-06-06)](#bsik) | 6 | 3 | 2 | 1 | 0 |
| [Swiss federal administration – Guidance sheet on LLMs (V1.0, 2024-04-26)](#bund) | 3 | 1 | 2 | 0 | 0 |
| [Checkmarx](#checkmarx) | 7 | 4 | 3 | 0 | 0 |
| [Data Protection Commissioner of the Canton of Zurich – Guidance sheet (V 1.0, April 2025)](#dsbzh) | 2 | 1 | 1 | 0 | 0 |
| [LinkedIn post (checklist)](#linkedin) | 5 | 1 | 4 | 0 | 0 |
| [Lumenalta – AI security checklist](#lumenalta) | 4 | 0 | 1 | 3 | 0 |
| [Myni Gmeind – AI guidance sheet (V2)](#myni) | 3 | 1 | 1 | 0 | 1 |
| [NCSC-UK / CISA – Guidelines for secure AI system development](#ncsc) | 12 | 5 | 7 | 0 | 0 |
| [NIST – AI RMF 1.0 / NIST AI 600-1 (GenAI profile)](#nist) | 2 | 2 | 0 | 0 | 0 |
| [OpenSSF – Security tips for AI coding assistants](#openssf) | 6 | 2 | 2 | 2 | 0 |
| [OWASP – AI Agent Security Cheat Sheet](#cheat) | 21 | 12 | 8 | 1 | 0 |
| [OWASP – AI Security Verification Standard (AISVS 1.0)](#aisvs) | 26 | 11 | 12 | 3 | 0 |
| [OWASP – Top 10 for LLM Applications (2025)](#llm) | 4 | 0 | 3 | 1 | 0 |
| [City of Zurich – AI directive (STRB 2281/2025)](#stzh) | 4 | 2 | 1 | 0 | 1 |
| [Swisscom – AI GRC for Swiss banks (July 2025)](#swisscom) | 5 | 2 | 2 | 1 | 0 |
| [Sysdig](#sysdig) | 4 | 2 | 2 | 0 | 0 |
| [Other](#doc) | 7 | 2 | 4 | 1 | 0 |

<a id="acalvio"></a>
### Acalvio

Source: [Acalvio](https://www.acalvio.com/agentic-ai-security/ai-security-ai-security-best-practices-nist-owasp-checklist/)

A vendor's best-practice checklist.

8 points: ✅ 1 · 🟡 planned 7 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Strict secret isolation | Item 5 | high | 🟡 planned: [sokar B140](https://github.com/sokar-ai/sokar/blob/main/issues/base/B140-A-Secret-Scan-Of-The-Workspace-At-Start.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):8-9, :15-22, :33-35; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):23-25; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):216-219; [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):31-33 | Provider and Git credentials never enter the container; what is in the repository the agent sees and can send to the provider. |
| Short-lived, narrowly scoped credentials per agent | Item 4/5 | high | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):15-23, :210-211, :220-237 | A token per task, `--token-hours` 8 h, ends with the task. |
| Detect exfiltration, alerts | Item 7 | high | 🟡 planned: [sokar B139](https://github.com/sokar-ai/sokar/blob/main/issues/base/B139-An-Alert-On-What-Looks-Wrong.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-34; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):179-180, :227 | Today only journalled: no detection, no baseline, no alert. |
| Logging and audit trail, tamper-evident | Item 14 | high | 🟡 planned: [sokar B154](https://github.com/sokar-ai/sokar/blob/main/issues/base/B154-A-Tamper-Evident-Event-Log-Per-Node.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):67; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):44-46; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):190-191; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):74-76 (hash-chained record, `talk verify`) | A machine-wide history is missing (sokar B26, B38). sokar B121 (decided, not built) removes the only tamper-evident record, and waits for the event log (sokar B154) that replaces it. |
| Kill switch, also for all agents, out of band | Item 10/11 | high | 🟡 planned: [sokar B146](https://github.com/sokar-ai/sokar/blob/main/issues/base/B146-One-Stop-Of-Every-Task-On-A-Node.md) [sokar B139](https://github.com/sokar-ai/sokar/blob/main/issues/base/B139-An-Alert-On-What-Looks-Wrong.md) | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):73-77 (`task stop`/`remove` auf dem Host), :194-195; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):22 | Today manual only (`task stop`/`remove` on the host, outside the agent's runtime). |
| Protect against the developer overriding policy | Item 1-3 | medium | 🟡 planned: [PJ16](PJ16-An-Organisation-Decides-Which-Providers-Its-Machines-Use.md) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):16-17, :48-54 | Today the developer can widen everything on their machine. |
| Adversarial testing / red teaming in CI | Item 12 | medium | 🟡 planned: [sokar B142](https://github.com/sokar-ai/sokar/blob/main/issues/base/B142-A-Misuse-Test-Suite-As-A-Release-Condition.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):14; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):160 | – |
| Inventory of agents, models, credentials | Item 1 | low | 🟡 planned: [sokar B156](https://github.com/sokar-ai/sokar/blob/main/issues/base/B156-sokar-inventory.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):95, :214-215, :350-361 | – |

<a id="acsc"></a>
### ACSC et al. – Engaging with Artificial Intelligence (January 2024)

Source: [BSI notice](https://www.bsi.bund.de/DE/Service-Navi/Presse/Alle-Meldungen-News/Meldungen/Leitfaden_KI-Systeme_230124.html); [ACSC «Engaging with AI»](https://www.cyber.gov.au/resources-business-and-government/governance-and-user-education/artificial-intelligence/engaging-with-artificial-intelligence)

With CISA, FBI, NSA, NCSC-UK, BSI and others.

7 points: ✅ 3 · 🟡 planned 2 · ❌ 1 · ⚪ 1

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Detect exfiltration, alerts | Topic «frequent repetitive prompts, baseline» | high | 🟡 planned: [sokar B139](https://github.com/sokar-ai/sokar/blob/main/issues/base/B139-An-Alert-On-What-Looks-Wrong.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-34; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):179-180, :227 | Today only journalled: no detection, no baseline, no alert. |
| Vendor dependency, data sovereignty (local model) | Topic «data residency, sovereignty» | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):59-86, :281-291 | A provider is a file, added without a Sokar release; a provider on one's own infrastructure with one's own CA. Choosing and running the model is the user's. |
| Exit strategy, concentration risk at the model vendor | – | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):59-90 | A provider is a file, `--provider` per run; switching needs no Sokar release. |
| Trial in a low-risk environment | Topic «trial» | medium | ✅ yes · see also [sokar B152](https://github.com/sokar-ai/sokar/blob/main/issues/base/B152-Say-What-Offline-Still-Reaches.md) | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):22-68; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):104-108; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):44-46 | Suitable as a pilot environment; the provider stays reachable, also in `offline`. |
| Review privileged access, lock after inactivity | – | medium | 🟡 planned: [sokar B153](https://github.com/sokar-ai/sokar/blob/main/issues/base/B153-Unused-Slots-And-Keys-Reported.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):347-353, :22 | Unused device slots and deploy keys are not locked automatically; sokar B117 only for clean-up. |
| MFA for access | – | low | ❌ no (deliberate) | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):137, :157-167; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):339-346 | Deliberate: MFA belongs to the operating system and ssh, the only ways into Sokar (running.md:137, :157-167). For weighty approvals: `sokar gate approve --signed` with a key on a hardware token (project-file.md:277-278). |
| Do not enter non-public data into external AI tools | Topic «privacy obligations» | – | ⚪ not relevant (the user's) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-33; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):26; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):97-101 | Which provider is used is the user's decision, outside Sokar. Sokar works with any provider, including one on the user's own infrastructure (credentials.md:59-90, :281-291), which Stadt Zürich's directive Art. 2 does not cover. |

<a id="bsit"></a>
### BSI – AI transparency checklist

Source: [BSI KI-Transparenz](https://www.bsi.bund.de/DE/Themen/Verbraucherinnen-und-Verbraucher/Informationen-und-Empfehlungen/Technologien_sicher_gestalten/Kuenstliche-Intelligenz/KI-Transparenz/ki-transparenz_node.html)

German Federal Office for Information Security.

4 points: ✅ 3 · 🟡 planned 1 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Least privilege, allowlists, no wildcards | Item 2 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):131-180, :213-239; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):44; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):16-27 | Inside the container the agent may do anything; the box replaces approval per command (corporate-security.md:62-64). |
| Make telemetry and data outflow visible | Item 1 | medium | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):42-46 | – |
| Only approved AI services / enterprise tiers | Item 6/7 | medium | 🟡 planned: [PJ16](PJ16-An-Organisation-Decides-Which-Providers-Its-Machines-Use.md) [sokar B151](https://github.com/sokar-ai/sokar/blob/main/issues/base/B151-The-Providers-A-Project-Allows.md) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):28-37, :103 | Contract and tier are the organisation's. |
| Updates and update management | Item 8 | low | ✅ yes | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):109-124; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):5-6 | Updates through the package manager (`apt`/`dnf`); applying them automatically (`unattended-upgrades`, `dnf-automatic`) is the user's. The daemon restarts itself after an update (running.md:109-124); running tasks deliberately only at their next start. A changelog exists; keeping it is sokar B55. |

<a id="bsik"></a>
### BSI – Criteria catalogue for AI in the federal administration (v1.0, 2025-06-06)

Source: [BSI notice on the catalogue](https://www.bsi.bund.de/DE/Service-Navi/Presse/Alle-Meldungen-News/Meldungen/Kriterienkatalog_KI_Bundesverwaltung_250624.html)

6 points: ✅ 3 · 🟡 planned 2 · ❌ 1 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Prompt injection defence | – | high | ❌ no (deliberate) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):77-90; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):3-8 | Deliberate: Sokar does not detect prompt injection but limits its consequences (cage, no key, only allowed hosts, gate). |
| Access decisions never in the model, separate from execution | Chapter 2.4.1 | high | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):12-14; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):140-157 | – |
| Threat model of the integration | Chapter 2.5 | high | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):1-8; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):77-90 | reach.md is structured by reach, hold, spend and produce, assuming a persuaded agent. |
| Remove sensitive data at the end | Chapter 2.7 | medium | ✅ yes | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):75-77; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):71-76; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):185 | Open: sokar B102, B117. |
| Label AI-generated results | Chapter 2.1, 2.6 | medium | 🟡 planned: [sokar B144](https://github.com/sokar-ai/sokar/blob/main/issues/base/B144-AI-Provenance-In-The-Commit.md) | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):95 | No label on the commit today; the author can be forged (reach.md:69). A commit trailer would meet it. |
| Inventory of AI applications with model and owner | Chapter 2.1 (MUST) | low | 🟡 planned: [sokar B156](https://github.com/sokar-ai/sokar/blob/main/issues/base/B156-sokar-inventory.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):95 | Today per machine and only while the task lives; near sokar B26. |

<a id="bund"></a>
### Swiss federal administration – Guidance sheet on LLMs (V1.0, 2024-04-26)

Source: linked from [Myni Gmeind](https://mynigmeind.ch/de/merkblaetter-sicherer-umgang-mit-kuenstlicher-intelligenz-in-der-oeffentlichen-verwaltung/)

3 points: ✅ 1 · 🟡 planned 2 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Control over data sent to external APIs | – | high | 🟡 planned: [sokar B149](https://github.com/sokar-ai/sokar/blob/main/issues/base/B149-A-Local-Provider-Template.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-33 | The channel to the provider is deliberately open; the provider is the user's choice. |
| Vendor dependency, data sovereignty (local model) | – | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):59-86, :281-291 | A provider is a file, added without a Sokar release; a provider on one's own infrastructure with one's own CA. Choosing and running the model is the user's. |
| Label AI-generated results | Principle 5 | medium | 🟡 planned: [sokar B144](https://github.com/sokar-ai/sokar/blob/main/issues/base/B144-AI-Provenance-In-The-Commit.md) | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):95 | No label on the commit today; the author can be forged (reach.md:69). A commit trailer would meet it. |

<a id="checkmarx"></a>
### Checkmarx

Source: [Checkmarx](https://checkmarx.com/learn/ai-security-ultimate-guide-to-securing-the-ai-ecosystem/)

A vendor's guide.

7 points: ✅ 4 · 🟡 planned 3 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| AI code is untrusted, human review | – | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):70-89, :90-104; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):60-79 | `guarded` (default): review at the gate on the host before the push, risky files first; `offline`: no work leaves; `online`: review on the forge before the merge (the task pushes only `sokar/<task>`, security.md:90-104). Assumption: in `online` the review happens on the forge. |
| Review by risk (risky files first) | – | high | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75-79 | CI definitions, build scripts, manifests, lockfiles, executables, symlinks first. An order, not detection. |
| Least privilege, allowlists, no wildcards | – | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):131-180, :213-239; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):44; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):16-27 | Inside the container the agent may do anything; the box replaces approval per command (corporate-security.md:62-64). |
| Human in the loop for critical actions | – | high | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):62-75; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):190-197; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):84-92 | Not bound to single tool calls; the control point is the result. |
| Slopsquatting: check suggested packages | – | high | 🟡 planned: [sokar B141](https://github.com/sokar-ai/sokar/blob/main/issues/base/B141-New-Dependencies-Checked-Before-Review.md) | [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):3-29, :72-73; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75-77 | Sokar does not check packages today; fetching can be limited to a curated mirror. |
| The same SAST/DAST gates for AI code in CI | – | medium | 🟡 planned: [sokar B95](https://github.com/sokar-ai/sokar/blob/main/issues/base/B95-A-Container-To-Try-Waiting-Work-In.md) | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):199-228; [sokar B95](https://github.com/sokar-ai/sokar/blob/main/issues/base/B95-A-Container-To-Try-Waiting-Work-In.md) (`gate try`, open) | Scanning is the project's CI; in `guarded` the forge builds only after approval (running.md:213). |
| Adversarial testing / red teaming in CI | – | medium | 🟡 planned: [sokar B142](https://github.com/sokar-ai/sokar/blob/main/issues/base/B142-A-Misuse-Test-Suite-As-A-Release-Condition.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):14; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):160 | – |

<a id="dsbzh"></a>
### Data Protection Commissioner of the Canton of Zurich – Guidance sheet (V 1.0, April 2025)

Source: [DSB ZH guidance sheet (PDF)](https://docs.datenschutz.ch/u/d/publikationen/formulare-merkblaetter/Merkblatt-Vorgehen-beim-Einsatz-von-KI-in-oeffentlichen-Organen.pdf)

2 points: ✅ 1 · 🟡 planned 1 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Only approved AI services / enterprise tiers | No. 3 | medium | 🟡 planned: [PJ16](PJ16-An-Organisation-Decides-Which-Providers-Its-Machines-Use.md) [sokar B151](https://github.com/sokar-ai/sokar/blob/main/issues/base/B151-The-Providers-A-Project-Allows.md) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):28-37, :103 | Contract and tier are the organisation's. |
| Data minimisation in the agent's context | No. 2 | medium | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):23-25; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):173-189 | The agent sees only the task's repository and files handed in on purpose (`/sokar/files`). What is in the repository is the user's; secrets in it are found by the workspace secret scan. |

<a id="linkedin"></a>
### LinkedIn post (checklist)

Source: [LinkedIn-Post](https://www.linkedin.com/posts/marchornbeek_ai-doesnt-write-secure-software-developers-activity-7409345628067176448-bGy0)

5 points: ✅ 1 · 🟡 planned 4 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| AI code is untrusted, human review | Item 12 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):70-89, :90-104; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):60-79 | `guarded` (default): review at the gate on the host before the push, risky files first; `offline`: no work leaves; `online`: review on the forge before the merge (the task pushes only `sokar/<task>`, security.md:90-104). Assumption: in `online` the review happens on the forge. |
| Strict secret isolation | Item 1/7 | high | 🟡 planned: [sokar B140](https://github.com/sokar-ai/sokar/blob/main/issues/base/B140-A-Secret-Scan-Of-The-Workspace-At-Start.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):8-9, :15-22, :33-35; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):23-25; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):216-219; [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):31-33 | Provider and Git credentials never enter the container; what is in the repository the agent sees and can send to the provider. |
| Slopsquatting: check suggested packages | Item 8 | high | 🟡 planned: [sokar B141](https://github.com/sokar-ai/sokar/blob/main/issues/base/B141-New-Dependencies-Checked-Before-Review.md) | [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):3-29, :72-73; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75-77 | Sokar does not check packages today; fetching can be limited to a curated mirror. |
| The same SAST/DAST gates for AI code in CI | Item 11 | medium | 🟡 planned: [sokar B95](https://github.com/sokar-ai/sokar/blob/main/issues/base/B95-A-Container-To-Try-Waiting-Work-In.md) | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):199-228; [sokar B95](https://github.com/sokar-ai/sokar/blob/main/issues/base/B95-A-Container-To-Try-Waiting-Work-In.md) (`gate try`, open) | Scanning is the project's CI; in `guarded` the forge builds only after approval (running.md:213). |
| Redact sensitive fields in logs | Item 10 | medium | 🟡 planned: [sokar B155](https://github.com/sokar-ai/sokar/blob/main/issues/base/B155-The-Logs-Checked-And-Documented.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):190-193, :120; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):74 | Whether prompts and contents are logged is not documented. |

<a id="lumenalta"></a>
### Lumenalta – AI security checklist

Source: [Lumenalta](https://lumenalta.com/insights/ai-security-checklist)

A service provider's blog article.

4 points: ✅ 0 · 🟡 planned 1 · ❌ 3 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Log of every permission change | Step 2 | medium | 🟡 planned: [sokar B154](https://github.com/sokar-ai/sokar/blob/main/issues/base/B154-A-Tamper-Evident-Event-Log-Per-Node.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):177-192, :198-202; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):91-92 | Project configuration changes only by signed commit; clearance answers are journalled; runtime extensions (`task clearance`, `--credential`) go into the event log. |
| Roles, RBAC, three lines model | Step 2 | medium | ❌ no (deliberate) · see also [sokar B145](https://github.com/sokar-ai/sokar/blob/main/issues/base/B145-Four-Eyes-At-The-Gate.md) | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):247; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):78-81 | Deliberate: a Sokar machine belongs to one user (how-it-works.md:247, running.md:78-81); roles and responsibilities are the operating system's and the organisation's. The four-eyes principle at the gate covers the part that touches Sokar. |
| MFA for access | Step 2 | low | ❌ no (deliberate) | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):137, :157-167; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):339-346 | Deliberate: MFA belongs to the operating system and ssh, the only ways into Sokar (running.md:137, :157-167). For weighty approvals: `sokar gate approve --signed` with a key on a hardware token (project-file.md:277-278). |
| Rotate keys on a schedule | Step 4 | low | ❌ no (deliberate) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):339-341, :164-167, :15-16; [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):241-251 | Deliberate: the keys stored in the vault are rotated by the user at each vendor; task tokens rotate with the task (credentials.md:15-16), refresh tokens at every renewal (:164-167). The risk to the vault itself is therefore low; the vault's master key is not rotated. |

<a id="myni"></a>
### Myni Gmeind – AI guidance sheet (V2)

Source: [Myni Gmeind](https://mynigmeind.ch/de/merkblaetter-sicherer-umgang-mit-kuenstlicher-intelligenz-in-der-oeffentlichen-verwaltung/)

3 points: ✅ 1 · 🟡 planned 1 · ❌ 0 · ⚪ 1

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Human in the loop for critical actions | – | high | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):62-75; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):190-197; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):84-92 | Not bound to single tool calls; the control point is the result. |
| Label AI-generated results | Section 5 | medium | 🟡 planned: [sokar B144](https://github.com/sokar-ai/sokar/blob/main/issues/base/B144-AI-Provenance-In-The-Commit.md) | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):95 | No label on the commit today; the author can be forged (reach.md:69). A commit trailer would meet it. |
| Do not enter non-public data into external AI tools | Section 2 | – | ⚪ not relevant (the user's) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-33; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):26; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):97-101 | Which provider is used is the user's decision, outside Sokar. Sokar works with any provider, including one on the user's own infrastructure (credentials.md:59-90, :281-291), which Stadt Zürich's directive Art. 2 does not cover. |

<a id="ncsc"></a>
### NCSC-UK / CISA – Guidelines for secure AI system development

Source: [NCSC Guidelines](https://www.ncsc.gov.uk/collection/guidelines-secure-ai-system-development)

12 points: ✅ 5 · 🟡 planned 7 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Logging and audit trail, tamper-evident | – | high | 🟡 planned: [sokar B154](https://github.com/sokar-ai/sokar/blob/main/issues/base/B154-A-Tamper-Evident-Event-Log-Per-Node.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):67; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):44-46; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):190-191; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):74-76 (hash-chained record, `talk verify`) | A machine-wide history is missing (sokar B26, B38). sokar B121 (decided, not built) removes the only tamper-evident record, and waits for the event log (sokar B154) that replaces it. |
| Supply chain of agent software and image | – | high | 🟡 planned: [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md) | [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):45; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):296-300; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):38-40; offen [sokar B98](https://github.com/sokar-ai/sokar/blob/main/issues/base/B98-The-Image-A-Signature-Names.md), [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md), [sokar B41](https://github.com/sokar-ai/sokar/blob/main/issues/base/B41-What-A-Packaged-Agent-Installs.md), [sokar B49](https://github.com/sokar-ai/sokar/blob/main/issues/base/B49-What-The-Build-Trusts-To-Run-Beside-Its-Secrets.md) | README.md shows `gpgcheck=0` (sokar B99). The image build does not run behind the firewall (how-it-works.md:222-223). |
| Threat model of the integration | «Model the threats» | high | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):1-8; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):77-90 | reach.md is structured by reach, hold, spend and produce, assuming a persuaded agent. |
| Secure by default, risky functions only by opt-in | Secure design | high | ✅ yes | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):104-108; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):23; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):102-103; [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):236-238 | – |
| Control over data sent to external APIs | Secure design | high | 🟡 planned: [sokar B149](https://github.com/sokar-ai/sokar/blob/main/issues/base/B149-A-Local-Provider-Template.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-33 | The channel to the provider is deliberately open; the provider is the user's choice. |
| Protect against the developer overriding policy | – | medium | 🟡 planned: [PJ16](PJ16-An-Organisation-Decides-Which-Providers-Its-Machines-Use.md) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):16-17, :48-54 | Today the developer can widen everything on their machine. |
| Restore a known good state, offline backups | assets, incident management | medium | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):50-51, :59-61 | The known good state is the Git repository, which the agent never changes directly; tasks can be discarded and restarted; `gate backup`/`restore` saves the mirror (security.md:50-51, :59-61). The vault and the journal belong in the machine's backup (documented by the data-protection section). |
| Treat logs as sensitive data | assets | medium | 🟡 planned: [sokar B155](https://github.com/sokar-ai/sokar/blob/main/issues/base/B155-The-Logs-Checked-And-Documented.md) | [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):75-76; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):190-193 | What the logs hold, where they lie and who can read them; file rights: sokar B50. |
| Vulnerability reporting process | lessons learned | medium | 🟡 planned: [PJ32](PJ32-A-Vulnerability-Has-A-Way-In.md) | – | – |
| Inventory of agents, models, credentials | – | low | 🟡 planned: [sokar B156](https://github.com/sokar-ai/sokar/blob/main/issues/base/B156-sokar-inventory.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):95, :214-215, :350-361 | – |
| Updates and update management | – | low | ✅ yes | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):109-124; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):5-6 | Updates through the package manager (`apt`/`dnf`); applying them automatically (`unattended-upgrades`, `dnf-automatic`) is the user's. The daemon restarts itself after an update (running.md:109-124); running tasks deliberately only at their next start. A changelog exists; keeping it is sokar B55. |
| Automatic updates by default | secure updates | low | ✅ yes | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):111-114 | Updates through the package manager (`apt`/`dnf`); applying them automatically (`unattended-upgrades`, `dnf-automatic`) is the user's. The daemon restarts itself after an update (running.md:109-124); running tasks deliberately only at their next start. |

<a id="nist"></a>
### NIST – AI RMF 1.0 / NIST AI 600-1 (GenAI profile)

Source: [NIST AI RMF](https://www.nist.gov/itl/ai-risk-management-framework)

2 points: ✅ 2 · 🟡 planned 0 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Vendor dependency, data sovereignty (local model) | NIST AI 600-1, Risk 4, 12 | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):59-86, :281-291 | A provider is a file, added without a Sokar release; a provider on one's own infrastructure with one's own CA. Choosing and running the model is the user's. |
| Automation bias in review | NIST AI 600-1, Risk 7 | medium | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):71-79 | The review shows the brief first, then the riskiest files, expressly as an order and not detection, with no seal of approval (reach.md:71-79). Strengthened by sokar B136 (approval bound to the seen commit), the full-diff guarantee and the four-eyes principle. |

<a id="openssf"></a>
### OpenSSF – Security tips for AI coding assistants

Source: [OpenSSF blog](https://openssf.org/blog/2025/12/29/ai-software-development-security-tips-and-the-future-part-1/)

6 points: ✅ 2 · 🟡 planned 2 · ❌ 2 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| AI code is untrusted, human review | Tip 5 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):70-89, :90-104; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):60-79 | `guarded` (default): review at the gate on the host before the push, risky files first; `offline`: no work leaves; `online`: review on the forge before the merge (the task pushes only `sokar/<task>`, security.md:90-104). Assumption: in `online` the review happens on the forge. |
| Least privilege, allowlists, no wildcards | Tip 1 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):131-180, :213-239; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):44; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):16-27 | Inside the container the agent may do anything; the box replaces approval per command (corporate-security.md:62-64). |
| Sandbox agents that execute code | Tip 2 (VM) | high | 🟡 planned: [sokar B59](https://github.com/sokar-ai/sokar/blob/main/issues/base/B59-A-Kernel-Of-Its-Own-For-A-Task.md) | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):38-45; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):143-157; [sokar B59](https://github.com/sokar-ai/sokar/blob/main/issues/base/B59-A-Kernel-Of-Its-Own-For-A-Task.md) (open) | Rootless container today; a VM as OpenSSF recommends is not; sokar B59 measures it first. |
| Prompt injection defence | Tip 3 | high | ❌ no (deliberate) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):77-90; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):3-8 | Deliberate: Sokar does not detect prompt injection but limits its consequences (cage, no key, only allowed hosts, gate). |
| Avoid the lethal trifecta | Tip 7 | high | ❌ no (deliberate) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):23-26; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):29-35 | Deliberate: private data is limited to one repository, communication to allowed hosts (corporate-security.md:23-26, reach.md:29-35). The channel to the provider stays open on purpose; the provider is the user's choice. Anomalies are caught by the alerting feature. |
| Slopsquatting: check suggested packages | Tip 6 | high | 🟡 planned: [sokar B141](https://github.com/sokar-ai/sokar/blob/main/issues/base/B141-New-Dependencies-Checked-Before-Review.md) | [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):3-29, :72-73; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75-77 | Sokar does not check packages today; fetching can be limited to a curated mirror. |

<a id="cheat"></a>
### OWASP – AI Agent Security Cheat Sheet

Source: [OWASP Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/AI_Agent_Security_Cheat_Sheet.html)

21 points: ✅ 12 · 🟡 planned 8 · ❌ 1 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| AI code is untrusted, human review | Section 4 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):70-89, :90-104; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):60-79 | `guarded` (default): review at the gate on the host before the push, risky files first; `offline`: no work leaves; `online`: review on the forge before the merge (the task pushes only `sokar/<task>`, security.md:90-104). Assumption: in `online` the review happens on the forge. |
| Review by risk (risky files first) | Section 4 | high | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75-79 | CI definitions, build scripts, manifests, lockfiles, executables, symlinks first. An order, not detection. |
| Short-lived, narrowly scoped credentials per agent | Section 4 | high | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):15-23, :210-211, :220-237 | A token per task, `--token-hours` 8 h, ends with the task. |
| Prevent credentials in answers / the agent minting tokens | Section 5 | high | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):43-47; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):40-55 | Known gap: the filter checks three field names only (credentials.md:52-55). |
| Least privilege, allowlists, no wildcards | Section 1 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):131-180, :213-239; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):44; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):16-27 | Inside the container the agent may do anything; the box replaces approval per command (corporate-security.md:62-64). |
| Sandbox agents that execute code | Section 9 | high | 🟡 planned: [sokar B59](https://github.com/sokar-ai/sokar/blob/main/issues/base/B59-A-Kernel-Of-Its-Own-For-A-Task.md) | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):38-45; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):143-157; [sokar B59](https://github.com/sokar-ai/sokar/blob/main/issues/base/B59-A-Kernel-Of-Its-Own-For-A-Task.md) (open) | Rootless container today; a VM as OpenSSF recommends is not; sokar B59 measures it first. |
| Fail closed | Section 1/Section 4 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):159-168; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):63-65; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):48-51 | The resolver starts deliberately degraded, the filter stays shut (security.md:267-272). |
| Human in the loop for critical actions | Section 4 | high | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):62-75; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):190-197; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):84-92 | Not bound to single tool calls; the control point is the result. |
| Bind approval to actor and action, not triggerable by the agent | Section 1/Section 4 | high | 🟡 planned: [sokar B136](https://github.com/sokar-ai/sokar/blob/main/issues/base/B136-An-Approve-Bound-To-What-Was-Reviewed.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):64; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):83-85 | Nothing the agent can reach calls approve. The interface, the plugin, `sokar approve` and `gate approve --commit` bind to the reviewed commit; a plain `sokar gate approve` does not yet. |
| Prompt injection defence | Section 2 | high | ❌ no (deliberate) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):77-90; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):3-8 | Deliberate: Sokar does not detect prompt injection but limits its consequences (cage, no key, only allowed hosts, gate). |
| Detect exfiltration, alerts | Section 5/Section 6 | high | 🟡 planned: [sokar B139](https://github.com/sokar-ai/sokar/blob/main/issues/base/B139-An-Alert-On-What-Looks-Wrong.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-34; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):179-180, :227 | Today only journalled: no detection, no baseline, no alert. |
| Logging and audit trail, tamper-evident | Section 6 | high | 🟡 planned: [sokar B154](https://github.com/sokar-ai/sokar/blob/main/issues/base/B154-A-Tamper-Evident-Event-Log-Per-Node.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):67; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):44-46; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):190-191; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):74-76 (hash-chained record, `talk verify`) | A machine-wide history is missing (sokar B26, B38). sokar B121 (decided, not built) removes the only tamper-evident record, and waits for the event log (sokar B154) that replaces it. |
| Rate limits, cost and token ceilings | Section 9 | high | 🟡 planned: [sokar B138](https://github.com/sokar-ai/sokar/blob/main/issues/base/B138-A-Cost-And-Token-Ceiling-Per-Task.md) [sokar B148](https://github.com/sokar-ai/sokar/blob/main/issues/base/B148-Token-Counting-Per-Task.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):126-127 (`per_day` pro Peer); [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):114-129 (Gate: 4 Anfragen, `503`); [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):106 | Rate limits exist for messages and the gate; a token and cost ceiling is missing. Cost is limited only by the provider account. |
| Multi-agent: trust boundaries, filter, sign, prevent replay | Section 7 | high | ✅ yes | [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):56-79; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):207-212; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):26; [sokar B122](https://github.com/sokar-ai/sokar/blob/main/issues/base/B122-No-Direct-Chat-Between-Tasks.md) | An expiry of messages is not described. sokar B108 (schema) is open. |
| Isolated execution environment per agent | Section 7 | high | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):26; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):133-136 | – |
| Redact sensitive fields in logs | Section 6/Section 8 | medium | 🟡 planned: [sokar B155](https://github.com/sokar-ai/sokar/blob/main/issues/base/B155-The-Logs-Checked-And-Documented.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):190-193, :120; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):74 | Whether prompts and contents are logged is not documented. |
| Encryption at rest / in transit | Section 8 | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):333-353, :17-19, :279-291 | The mirror with unreviewed work is not encrypted; sokar B50 covers file rights. |
| Data minimisation in the agent's context | Section 8 | medium | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):23-25; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):173-189 | The agent sees only the task's repository and files handed in on purpose (`/sokar/files`). What is in the repository is the user's; secrets in it are found by the workspace secret scan. |
| Adversarial testing / red teaming in CI | Section 10 | medium | 🟡 planned: [sokar B142](https://github.com/sokar-ai/sokar/blob/main/issues/base/B142-A-Misuse-Test-Suite-As-A-Release-Condition.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):14; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):160 | – |
| Structured inference log, token count per session | Section 6 | medium | 🟡 planned: [sokar B148](https://github.com/sokar-ai/sokar/blob/main/issues/base/B148-Token-Counting-Per-Task.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):33; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):95 | Format and token count are not documented; no monitoring of covert channels (12.2.6). |
| Protect the agent's memory | Section 3 | low | ✅ yes | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):73-80; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):71-73 | The agent's memory lives in the task's container, separate from other tasks and the host, and ends with `task remove` (how-it-works.md:73-80). How it is managed is the agent's. |

<a id="aisvs"></a>
### OWASP – AI Security Verification Standard (AISVS 1.0)

Source: [OWASP AISVS](https://owasp.org/projects/artificial-intelligence-security-verification-standard-aisvs-docs)

26 points: ✅ 11 · 🟡 planned 12 · ❌ 3 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Rate limits, cost and token ceilings | Requirement 9.1.2 | high | 🟡 planned: [sokar B138](https://github.com/sokar-ai/sokar/blob/main/issues/base/B138-A-Cost-And-Token-Ceiling-Per-Task.md) [sokar B148](https://github.com/sokar-ai/sokar/blob/main/issues/base/B148-Token-Counting-Per-Task.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):126-127 (`per_day` pro Peer); [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):114-129 (Gate: 4 Anfragen, `503`); [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):106 | Rate limits exist for messages and the gate; a token and cost ceiling is missing. Cost is limited only by the provider account. |
| Kill switch, also for all agents, out of band | Requirement 9.1.3, 9.6.1, 9.6.3 | high | 🟡 planned: [sokar B146](https://github.com/sokar-ai/sokar/blob/main/issues/base/B146-One-Stop-Of-Every-Task-On-A-Node.md) [sokar B139](https://github.com/sokar-ai/sokar/blob/main/issues/base/B139-An-Alert-On-What-Looks-Wrong.md) | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):73-77 (`task stop`/`remove` auf dem Host), :194-195; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):22 | Today manual only (`task stop`/`remove` on the host, outside the agent's runtime). |
| Supply chain of agent software and image | Chapter C06 | high | 🟡 planned: [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md) | [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):45; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):296-300; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):38-40; offen [sokar B98](https://github.com/sokar-ai/sokar/blob/main/issues/base/B98-The-Image-A-Signature-Names.md), [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md), [sokar B41](https://github.com/sokar-ai/sokar/blob/main/issues/base/B41-What-A-Packaged-Agent-Installs.md), [sokar B49](https://github.com/sokar-ai/sokar/blob/main/issues/base/B49-What-The-Build-Trusts-To-Run-Beside-Its-Secrets.md) | README.md shows `gpgcheck=0` (sokar B99). The image build does not run behind the firewall (how-it-works.md:222-223). |
| MCP / tool poisoning, rug pull | Chapter C10 | high | 🟡 planned: [PJ15](PJ15-A-Tool-Does-Not-Change-Behind-The-Persons-Back.md) | [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):79-100; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):92-97, :105 | Damage is limited today, detection is missing; the protocols: PJ24. |
| The agent cannot change its own rules | Requirement 9.2.5 | high | ✅ yes | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):183-185; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):50-51; [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):95-96 | The agent can only propose `project.yml`; a person signs it. |
| Access decisions never in the model, separate from execution | Requirement 9.5.3, 5.2.5 | high | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):12-14; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):140-157 | – |
| Show the approver the full action | Requirement 9.2.2 | high | 🟡 planned: [sokar B150](https://github.com/sokar-ai/sokar/blob/main/issues/base/B150-The-Whole-Diff-In-Review.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75-79 | Whether long diffs are cut is not documented. |
| Separate untrusted data from tool calls | Requirement 9.3.5, 9.3.6 | high | ❌ no (deliberate) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):6-8 | Deliberate: the same agent reads foreign content and acts; Sokar limits the consequences instead of separating them (reach.md:6-8). A separation would be the agent's. |
| Sandbox locally started MCP servers, only allowed MCP servers | Requirement 10.1.2, 10.1.3 | high | 🟡 planned: [PJ15](PJ15-A-Tool-Does-Not-Change-Behind-The-Persons-Back.md) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):59-60; [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):89-90, :98-100 | Local MCP servers run in the box but are not checked. |
| Agents cannot approve, merge or deploy their own work or bypass branch protection | Requirement AC.8.1, AC.8.3 | high | ✅ yes | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):64; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):94-98 | – |
| No credentials in the workspace with AI code | Requirement AC.12.2 | high | 🟡 planned: [sokar B140](https://github.com/sokar-ai/sokar/blob/main/issues/base/B140-A-Secret-Scan-Of-The-Workspace-At-Start.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):33-35; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):47-48 | Sokar puts none there; a token in the repository itself stays visible (how-it-works.md:216-219). |
| Resource abuse, limits per execution (CPU, memory, processes) | Requirement 9.1.1 | medium | ✅ yes · see also [sokar B147](https://github.com/sokar-ai/sokar/blob/main/issues/base/B147-A-Wall-Clock-Limit-For-Unattended-Runs.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):109-114 (Default `memory: 8g`, `pids: 2048`, `cpus: "2.0"`, `hand_in: 64m`), :69-72; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):124-126 | Defaults `memory: 8g`, `pids: 2048`, `cpus: "2.0"` (project-file.md:109-114). |
| Unique identity per agent instance | Requirement 9.4.1 | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):15-16; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):43-44; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):49-54 | No scheduled rotation; the token ends with the task. |
| Bind approval cryptographically to the action | Requirement 9.2.8, 9.2.9 | medium | 🟡 planned: [sokar B136](https://github.com/sokar-ai/sokar/blob/main/issues/base/B136-An-Approve-Bound-To-What-Was-Reviewed.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):64; [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):277-278 (`gate approve --signed`, only with enroll) | `gate approve --signed` with an enrolled key (project-file.md:277-278); binding a plain approve to the reviewed commit is sokar B136. |
| An approval timeout blocks the action | Requirement 9.6.2 | medium | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):72-74; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):91-92 | Work waits at the gate until someone approves (security.md:72-74). A clearance prompt with no answer within 60 s is a deny; the destination stays blocked for the run (tested; the doc gets a sentence). |
| Do not pass client tokens to downstream APIs | Requirement 10.2.7 | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):15-22, :176-177, :210-211 | – |
| Detect encoded content smuggling instructions | Requirement 2.1.2 | medium | ❌ no (deliberate) | [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):162-165; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):207-209 | Deliberate: Sokar checks encoded content only in messages between tasks (the sluice, messages.md:162-165). For the repository and the web the same holds as for prompt injection: Sokar limits consequences instead of detecting. |
| Structured inference log, token count per session | Requirement 12.1.3, 12.2.5 | medium | 🟡 planned: [sokar B148](https://github.com/sokar-ai/sokar/blob/main/issues/base/B148-Token-Counting-Per-Task.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):33; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):95 | Format and token count are not documented; no monitoring of covert channels (12.2.6). |
| Audit of approvals with person, time, result | Requirement 12.4.2 | medium | 🟡 planned: [sokar B154](https://github.com/sokar-ai/sokar/blob/main/issues/base/B154-A-Tamper-Evident-Event-Log-Per-Node.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):91-92; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):83-85; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):190-191 | The approver's identity is not recorded; near sokar B26. |
| Isolation between tenants | Requirement 5.3.1 | medium | ✅ yes | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):78-81; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):26 | – |
| Four-eyes principle (review by someone other than the requester) | Requirement AC.4.1, AC.8.4 | medium | 🟡 planned: [sokar B145](https://github.com/sokar-ai/sokar/blob/main/issues/base/B145-Four-Eyes-At-The-Gate.md) | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):247; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):16-17 | Today whoever starts the task may approve it. |
| Provenance and AI metadata on the artefact | Requirement AC.5.1, AC.9, AC.10 | medium | 🟡 planned: [sokar B144](https://github.com/sokar-ai/sokar/blob/main/issues/base/B144-AI-Provenance-In-The-Commit.md) | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):73, :95; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):223-224 | No commit trailer, no AI-BOM with model and provider today. |
| Revoke an agent's identity quickly, rotate secrets | Requirement AC.14.2, AC.14.3 | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):22, :360, :152-153 | The agent holds only a stand-in token that ends with the task; a stop revokes everything at once (credentials.md:22). The agent never sees the real keys; devices are revoked with `vault revoke` (:360). |
| Protect the agent's memory | Chapter C08 | low | ✅ yes | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):73-80; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):71-73 | The agent's memory lives in the task's container, separate from other tasks and the host, and ends with `task remove` (how-it-works.md:73-80). How it is managed is the agent's. |
| AI-assisted review as a supplement | Requirement 9.2.6, 9.2.7 | low | ❌ no (deliberate) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):73 | Deliberate: fixed rules, "not another model". |
| Short-lived execution environments | Requirement AC.12.4 | low | ✅ yes | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):73-77 | Every task starts in a fresh container and shares nothing with others; with `--rm` it is removed after the run. That a task without `--rm` stays is intended, for longer work with interruptions (how-it-works.md:73-77). |

<a id="llm"></a>
### OWASP – Top 10 for LLM Applications (2025)

Source: [OWASP Top 10 for LLM](https://genai.owasp.org/llm-top-10/)

4 points: ✅ 0 · 🟡 planned 3 · ❌ 1 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Prompt injection defence | Risk LLM01 | high | ❌ no (deliberate) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):77-90; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):3-8 | Deliberate: Sokar does not detect prompt injection but limits its consequences (cage, no key, only allowed hosts, gate). |
| Rate limits, cost and token ceilings | Risk LLM10 | high | 🟡 planned: [sokar B138](https://github.com/sokar-ai/sokar/blob/main/issues/base/B138-A-Cost-And-Token-Ceiling-Per-Task.md) [sokar B148](https://github.com/sokar-ai/sokar/blob/main/issues/base/B148-Token-Counting-Per-Task.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):126-127 (`per_day` pro Peer); [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):114-129 (Gate: 4 Anfragen, `503`); [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):106 | Rate limits exist for messages and the gate; a token and cost ceiling is missing. Cost is limited only by the provider account. |
| Supply chain of agent software and image | Risk LLM03 | high | 🟡 planned: [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md) | [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):45; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):296-300; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):38-40; offen [sokar B98](https://github.com/sokar-ai/sokar/blob/main/issues/base/B98-The-Image-A-Signature-Names.md), [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md), [sokar B41](https://github.com/sokar-ai/sokar/blob/main/issues/base/B41-What-A-Packaged-Agent-Installs.md), [sokar B49](https://github.com/sokar-ai/sokar/blob/main/issues/base/B49-What-The-Build-Trusts-To-Run-Beside-Its-Secrets.md) | README.md shows `gpgcheck=0` (sokar B99). The image build does not run behind the firewall (how-it-works.md:222-223). |
| MCP / tool poisoning, rug pull | Risk LLM06 | high | 🟡 planned: [PJ15](PJ15-A-Tool-Does-Not-Change-Behind-The-Persons-Back.md) | [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):79-100; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):92-97, :105 | Damage is limited today, detection is missing; the protocols: PJ24. |

<a id="stzh"></a>
### City of Zurich – AI directive (STRB 2281/2025)

Source: [STRB 2281/2025](https://www.stadt-zuerich.ch/de/politik-und-verwaltung/politik-und-recht/stadtratsbeschluesse/2025/08/stzh-strb-2025-2281.html)

In force since 2025-09-01.

4 points: ✅ 2 · 🟡 planned 1 · ❌ 0 · ⚪ 1

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| AI code is untrusted, human review | AI directive Art. 9 let. h, Art. 10 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):70-89, :90-104; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):60-79 | `guarded` (default): review at the gate on the host before the push, risky files first; `offline`: no work leaves; `online`: review on the forge before the merge (the task pushes only `sokar/<task>`, security.md:90-104). Assumption: in `online` the review happens on the forge. |
| Vendor dependency, data sovereignty (local model) | AI directive Art. 2 | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):59-86, :281-291 | A provider is a file, added without a Sokar release; a provider on one's own infrastructure with one's own CA. Choosing and running the model is the user's. |
| Label AI-generated results | AI directive Art. 11 para. 1 | medium | 🟡 planned: [sokar B144](https://github.com/sokar-ai/sokar/blob/main/issues/base/B144-AI-Provenance-In-The-Commit.md) | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):95 | No label on the commit today; the author can be forged (reach.md:69). A commit trailer would meet it. |
| Do not enter non-public data into external AI tools | AI directive Art. 6, 7 | – | ⚪ not relevant (the user's) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):31-33; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):26; [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):97-101 | Which provider is used is the user's decision, outside Sokar. Sokar works with any provider, including one on the user's own infrastructure (credentials.md:59-90, :281-291), which Stadt Zürich's directive Art. 2 does not cover. |

<a id="swisscom"></a>
### Swisscom – AI GRC for Swiss banks (July 2025)

Source: [Swisscom](https://www.swisscom.ch/de/business/enterprise/downloads/banking/ai-governance-compliance-fuer-schweizer-banken.html)

5 points: ✅ 2 · 🟡 planned 2 · ❌ 1 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| AI code is untrusted, human review | Chapter 5.3.2 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):70-89, :90-104; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):60-79 | `guarded` (default): review at the gate on the host before the push, risky files first; `offline`: no work leaves; `online`: review on the forge before the merge (the task pushes only `sokar/<task>`, security.md:90-104). Assumption: in `online` the review happens on the forge. |
| Logging and audit trail, tamper-evident | Chapter 3.3.1-3.3.3 (EU AI Act Art. 11/12) | high | 🟡 planned: [sokar B154](https://github.com/sokar-ai/sokar/blob/main/issues/base/B154-A-Tamper-Evident-Event-Log-Per-Node.md) | [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):67; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):44-46; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):190-191; [doc/messages.md](https://github.com/sokar-ai/sokar/blob/main/doc/messages.md):74-76 (hash-chained record, `talk verify`) | A machine-wide history is missing (sokar B26, B38). sokar B121 (decided, not built) removes the only tamper-evident record, and waits for the event log (sokar B154) that replaces it. |
| Exit strategy, concentration risk at the model vendor | Chapter 3.1.8, 3.1.10 | medium | ✅ yes | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):59-90 | A provider is a file, `--provider` per run; switching needs no Sokar release. |
| Log of every permission change | Chapter 3.3.2 | medium | 🟡 planned: [sokar B154](https://github.com/sokar-ai/sokar/blob/main/issues/base/B154-A-Tamper-Evident-Event-Log-Per-Node.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):177-192, :198-202; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):91-92 | Project configuration changes only by signed commit; clearance answers are journalled; runtime extensions (`task clearance`, `--credential`) go into the event log. |
| Roles, RBAC, three lines model | Chapter 4.1, 5.2.1 | medium | ❌ no (deliberate) · see also [sokar B145](https://github.com/sokar-ai/sokar/blob/main/issues/base/B145-Four-Eyes-At-The-Gate.md) | [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):247; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):78-81 | Deliberate: a Sokar machine belongs to one user (how-it-works.md:247, running.md:78-81); roles and responsibilities are the operating system's and the organisation's. The four-eyes principle at the gate covers the part that touches Sokar. |

<a id="sysdig"></a>
### Sysdig

Source: [Sysdig](https://www.sysdig.com/learn-cloud-native/top-8-ai-security-best-practices)

A vendor's list.

4 points: ✅ 2 · 🟡 planned 2 · ❌ 0 · ⚪ 0

| Point | Where in the guideline | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| Least privilege, allowlists, no wildcards | Item 8 | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):131-180, :213-239; [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):44; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):16-27 | Inside the container the agent may do anything; the box replaces approval per command (corporate-security.md:62-64). |
| Rate limits, cost and token ceilings | Item 7/8 | high | 🟡 planned: [sokar B138](https://github.com/sokar-ai/sokar/blob/main/issues/base/B138-A-Cost-And-Token-Ceiling-Per-Task.md) [sokar B148](https://github.com/sokar-ai/sokar/blob/main/issues/base/B148-Token-Counting-Per-Task.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):126-127 (`per_day` pro Peer); [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):114-129 (Gate: 4 Anfragen, `503`); [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):106 | Rate limits exist for messages and the gate; a token and cost ceiling is missing. Cost is limited only by the provider account. |
| Supply chain of agent software and image | Item 6 | high | 🟡 planned: [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md) | [sokar README](https://github.com/sokar-ai/sokar/blob/main/README.md):45; [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):296-300; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):38-40; offen [sokar B98](https://github.com/sokar-ai/sokar/blob/main/issues/base/B98-The-Image-A-Signature-Names.md), [sokar B99](https://github.com/sokar-ai/sokar/blob/main/issues/base/B99-Packages-Signed-And-Checked.md), [sokar B41](https://github.com/sokar-ai/sokar/blob/main/issues/base/B41-What-A-Packaged-Agent-Installs.md), [sokar B49](https://github.com/sokar-ai/sokar/blob/main/issues/base/B49-What-The-Build-Trusts-To-Run-Beside-Its-Secrets.md) | README.md shows `gpgcheck=0` (sokar B99). The image build does not run behind the firewall (how-it-works.md:222-223). |
| Resource abuse, limits per execution (CPU, memory, processes) | Item 8 | medium | ✅ yes · see also [sokar B147](https://github.com/sokar-ai/sokar/blob/main/issues/base/B147-A-Wall-Clock-Limit-For-Unattended-Runs.md) | [doc/project-file.md](https://github.com/sokar-ai/sokar/blob/main/doc/project-file.md):109-114 (Default `memory: 8g`, `pids: 2048`, `cpus: "2.0"`, `hand_in: 64m`), :69-72; [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):124-126 | Defaults `memory: 8g`, `pids: 2048`, `cpus: "2.0"` (project-file.md:109-114). |

<a id="doc"></a>
### Other

Single points not tied to one guideline.

7 points: ✅ 2 · 🟡 planned 4 · ❌ 1 · ⚪ 0

| Point | Source | Relevance | Met? | Evidence in Sokar | Remark |
|---|---|---|---|---|---|
| AI code is untrusted, human review | source unknown | high | ✅ yes | [doc/security.md](https://github.com/sokar-ai/sokar/blob/main/doc/security.md):70-89, :90-104; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):60-79 | `guarded` (default): review at the gate on the host before the push, risky files first; `offline`: no work leaves; `online`: review on the forge before the merge (the task pushes only `sokar/<task>`, security.md:90-104). Assumption: in `online` the review happens on the forge. |
| Strict secret isolation | source unknown | high | 🟡 planned: [sokar B140](https://github.com/sokar-ai/sokar/blob/main/issues/base/B140-A-Secret-Scan-Of-The-Workspace-At-Start.md) | [doc/credentials.md](https://github.com/sokar-ai/sokar/blob/main/doc/credentials.md):8-9, :15-22, :33-35; [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):23-25; [doc/how-it-works.md](https://github.com/sokar-ai/sokar/blob/main/doc/how-it-works.md):216-219; [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):31-33 | Provider and Git credentials never enter the container; what is in the repository the agent sees and can send to the provider. |
| Prompt injection defence | [OWASP LLM01 Prompt Injection](https://genai.owasp.org/llmrisk/llm01-prompt-injection/) | high | ❌ no (deliberate) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):77-90; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):3-8 | Deliberate: Sokar does not detect prompt injection but limits its consequences (cage, no key, only allowed hosts, gate). |
| Slopsquatting: check suggested packages | source unknown | high | 🟡 planned: [sokar B141](https://github.com/sokar-ai/sokar/blob/main/issues/base/B141-New-Dependencies-Checked-Before-Review.md) | [doc/faq.md](https://github.com/sokar-ai/sokar/blob/main/doc/faq.md):3-29, :72-73; [doc/reach.md](https://github.com/sokar-ai/sokar/blob/main/doc/reach.md):75-77 | Sokar does not check packages today; fetching can be limited to a curated mirror. |
| The same SAST/DAST gates for AI code in CI | source unknown | medium | 🟡 planned: [sokar B95](https://github.com/sokar-ai/sokar/blob/main/issues/base/B95-A-Container-To-Try-Waiting-Work-In.md) | [doc/running.md](https://github.com/sokar-ai/sokar/blob/main/doc/running.md):199-228; [sokar B95](https://github.com/sokar-ai/sokar/blob/main/issues/base/B95-A-Container-To-Try-Waiting-Work-In.md) (`gate try`, open) | Scanning is the project's CI; in `guarded` the forge builds only after approval (running.md:213). |
| Make telemetry and data outflow visible | source unknown | medium | ✅ yes | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):42-46 | – |
| Only approved AI services / enterprise tiers | source unknown | medium | 🟡 planned: [PJ16](PJ16-An-Organisation-Decides-Which-Providers-Its-Machines-Use.md) [sokar B151](https://github.com/sokar-ai/sokar/blob/main/issues/base/B151-The-Providers-A-Project-Allows.md) | [doc/corporate-security.md](https://github.com/sokar-ai/sokar/blob/main/doc/corporate-security.md):28-37, :103 | Contract and tier are the organisation's. |

## Not relevant to Sokar

- ⚪ Training-data poisoning, adversarial training, differential privacy (Sysdig 1/2/4, AISVS C01/C11,
  OWASP LLM04): Sokar trains no models.
- ⚪ Vector database and embedding security (LLM08, AISVS C08): Sokar has none.
- ⚪ System prompt leakage, misinformation, content-safety filters (LLM07, LLM09, Cheat Sheet §5): the
  model's or the agent's, not the sandbox's.
- ⚪ Protecting the intellectual property of models (Sysdig 3): Sokar hosts no models.
- ⚪ Single secure-coding rules (parameterised queries, output encoding, TLS hardening, server-side
  auth; LinkedIn 2-6, 9): the duty of the project whose code the agent writes.
- ⚪ Enterprise tier and contractual no-training clause, data processing agreements, DPIA, prior
  checks (DSB ZH), FINMA and the EU AI Act (Swisscom), NIST AI RMF and governance boards:
  organisational or contractual. Sokar can at most supply the technical measures for an ISDS concept.
- ⚪ BSI AI transparency items 3-5 (purpose, knowledge cut-off, user help): for end users of an AI tool.
- ⚪ AISVS C01, C03, C06 (training data, model lifecycle, model artefacts and AI-BOM for weights) and
  C04.2-C04.3 (GPU, TEE, edge): Sokar trains and hosts no models.
- ⚪ AISVS C07, C12.3, 2.2.x (confidence, hallucination rate, content classifiers, watermarking,
  content scoring): the model's or the application's.
- ⚪ NIST AI 600-1 risks 1, 3, 5, 6, 8, 10, 11 (CBRN, dangerous content, environment, bias, information
  integrity, IP, abusive content): model risks unrelated to isolation.
- ⚪ NIST AI RMF GOVERN/MAP/MEASURE/MANAGE: an organisational framework; Sokar supplies evidence at most.
- ⚪ BSI catalogue 2.1-2.3 (AI officers, terms of use, training, vendor assessment, procurement, AIC4
  attestations): duties of the operating organisation.
- ⚪ Myni Gmeind §3-§6 and the federal LLM sheet (permitted uses, fairness, legal basis, training):
  addressed to staff.
- ⚪ AISVS AC.6 (feedback, fine-tuning), AC.13.1/13.4-13.6 (analysis of incoming PRs): the forge's or
  the model operator's.
- ⚪ Swisscom chapter 2 (FINMA circulars, BankG, FIDLEG, DSG/DPIA, EU AI Act classes, ISO 42001/23894/
  27001), chapters 3.1-3.2 (model, data, explainability, reputation risk), chapters 4-5 (boards, RACI,
  maturity, GRC platforms): a bank's regulation and organisation.
- ⚪ ACSC: data poisoning, model stealing, backups of model and training data, data drift.
- ⚪ ACSC, Lumenalta step 7, Swisscom 5.3.1: incident response plan, SLAs, tabletop exercises
  (organisational).
- ⚪ Lumenalta steps 1, 3, 6: stakeholders, training data, penetration tests, training.
- ⚪ City of Zurich directive Art. 4 (the vendor account), Art. 8, 9, 12 (permitted uses, liability),
  Art. 9 let. g (copyright of the code entered: the organisation's decision about the repository).
