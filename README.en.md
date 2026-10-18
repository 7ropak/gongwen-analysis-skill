# Gongwen Analysis

[简体中文](README.md) | English | [繁體中文](README.zh-TW.md) | v1.0.0

> **Gongwen Analysis** — a skill pack for analyzing corporate public documents.
> Give it an announcement, an earnings report, or a crisis statement, and it works out how each side will react; give it an event, and it works backward to what can be said and how far you can commit.

Works with: Claude Code, Codex, WorkBuddy, Doubao, and any other agent that can load Markdown instruction files.

---

## What this skill pack does

Feed it a corporate document — a listed-company announcement, a periodic report, a clarification notice, a regulatory inquiry reply, a crisis statement, an apology letter, a PR release, an internal memo, an ESG report — and it returns a layered read: what the text actually says, what each side cares about, where their bottom lines are, how they are likely to react, and which signals to watch next.

It does five things:

- **Break down the structure**: identify the document type, apply its skeleton, and find the components that should be there but aren't — a missing piece is itself a signal.
- **Build the factual base**: timeline, subject, prior record, peer comparison, each fact tagged with source and credibility tier; if something can't be found, it says "not found" instead of filling the gap with guesswork.
- **Model the people**: nine stakeholder groups (public, regulators, capital, customers, employees, suppliers and channels, media, community, competitors), ranked by power / legitimacy / urgency, with trigger conditions, direction, intensity, and timing — plus whether they will coalesce and amplify.
- **Mark the wording and the numbers**: label the strategy behind each statement sentence by sentence and check internal consistency; for financial documents, layer account-level and disclosure signals on top, read against how the phrasing is actually used in practice.
- **Deliver a conclusion**: worst-case outcome, verifiable commitments, observation indicators. Long-running events can be opened as a tracking case and re-checked on a schedule with facts filled back in.

It also runs in reverse: given only an event and no document, it switches to the issuing side and produces a three-way commitment boundary (can commit / cannot commit / must escalate), a draft statement, and pre-release approval prompts.

Four modes: **quick read** (what this document says, within one page), **standard analysis** (full report plus independent review, the default), **tracking re-check** (compared with the previous period), **positioning review** (issuing side).

Two agents and nine skills do this work together. The criteria live in a knowledge base and are read on demand, never loaded wholesale into context.

## How to use it

Plain Markdown and YAML. No runtime requirements, no dependencies to install. Put the directory wherever your agent can read it.

### Route A: hand the repo to your agent (recommended)

> `https://github.com/7ropak/gongwen-analysis-skill`

Your agent will parse the repository itself. The entry point is `agents/公文分析Agent.md`.

### Route B: clone locally, then connect

```
git clone https://github.com/7ropak/gongwen-analysis-skill.git
```

Then hand your agent this instruction: read `gongwen-analysis-skill/agents/公文分析Agent.md` and follow the entry protocol defined there.

At call time, three things are enough:

```
Call 公文分析Agent (Official Document Analysis Agent):
 Document: [paste text / link / file]
 Subject: Company X (optional)
 Mode: standard
```

For the issuing side, replace the first field with `Event: ...` and set `Mode: positioning review`. Optional fields — time window, listing venue, stakeholder groups of interest — are defined in `schemas/分析输入.schema.yaml`.

## What's inside

- `agents/` — two agents: `公文分析Agent.md` is the entry point and orchestrates the pipeline; `分析审计Agent.md` runs the review in an independent context.
- `skills/` — nine skills, one per stage: document type and structure parsing, event background research, stakeholder modeling, statement pragmatics annotation, financial and compliance signal scanning, report generation, tracking case management, positioning review and drafting, lexicon maintenance.
- `schemas/` — five YAML contracts: analysis input, analysis output, positioning review, tracking case, experience configuration. Field definitions and section order follow the contracts.
- `knowledge-base/` — the criteria library: document type library, stakeholder baseline, statement strategy library, crisis response process, signal library, subtext library, the three lexicon volumes (escalation ladder of stance, corporate statement wording, hedging and qualifier wording), and user experience notes.
- `prompts/` — reusable criteria fragments and output templates, limited to what the agents and skills actually reference.
- `docs/` — information architecture: directory responsibilities and where outputs go.

At runtime it also creates `output/` (delivery reports), `debug/` (process records), and `data/` (raw research notes and tracking cases). These three are not shipped with the repository.

Can you change it? Everything except `schemas/` is plain text. Edit lexicon entries, add stakeholder groups, swap templates, or adjust criteria by editing the corresponding file. If you change a contract, update the skills that reference it.

## How it works

A document comes in, and the first decision is its type and legal footing — mandatory disclosure, self-regulatory requirement, voluntary disclosure, or no obligation. That tier determines which methods may serve as the primary test and which can only assist; get it wrong and the pragmatics analysis drifts as a whole. Next comes the factual base: timeline, subject, prior record, peers, each with a source. Then the people are placed inside the frame, ranked by priority, and checked for who moves first and whether coalitions amplify the reaction. At the text layer, stance and hedging are annotated sentence by sentence; for financial documents, account-level and disclosure signals are layered on. Everything converges on the conclusion layer: worst case, verifiable commitments, and the points in time to watch. Events worth following become tracking cases, with facts filled back in and re-checked each period.

The reading side and the issuing side run in opposite directions, but share the same stakeholder baseline and the same strategy numbering — which is why "how others will read it" and "how I should write it" line up.

## Design choices

- **Two layers of output** — the delivery report holds results only; the debug file holds criteria and check records. Final readers want to know what was said and what follows, and too many methodological traces make inference look like conclusion; anyone who needs to audit has the full process separately.
- **The lexicon is a criterion, not a dictionary** — "regret" and "apology" sit one notch apart, and that notch determines what the other side does next. Writing those distinctions down as comparable entries is what keeps the analysis off pure gut feeling.
- **Contracts first** — five schemas define input, output, tracking, experience, and positioning fields. Section order and naming follow the contracts alone, so reports of the same kind don't come out differently each time.
- **Both directions share one set of criteria** — the two sides run opposite ways but cite the same stakeholder baseline and strategy numbering, so a drafted statement can survive your own simulation.
- **Review is independent of analysis** — the reviewer reads the source document first and forms a judgment, then compares it with the conclusions, without reading the analyst's intermediate reasoning; a shared context inherits the same misreading.
- **Probabilities come in tiers, not percentages** — without calibration samples, precise numbers are false certainty; a tier plus observation indicators is what can actually be verified.

## Documentation

- [Project overview](project-overview.md) — goals, scope, glossary, known risks
- [Agent index](agents/README.md) | [Skill index](skills/README.md)
- [Knowledge base index](knowledge-base/README.md) — what each library holds, its tier, and when to consult it
- [Information architecture](docs/IA.md) — directory responsibilities and where outputs belong

## Scope and limits

- **Not investment advice**: no ratings, target prices, valuations, or timing calls.
- **Not legal advice**: conclusions touching litigation or regulation are written only as "items to verify + how to verify them", never as legal characterization.
- Uses only the text you provide and public information; does not retrieve non-public material.
- The subtext list and quantitative thresholds are **empirical conventions**, not regulatory rules, and must not be used as a compliance basis.
- Without calibration samples, inferences are marked uncalibrated and are less reliable than the factual layer; use them alongside primary materials.
- Issuing-side output is **internal decision support**, not a finished document for external release; it does not produce false statements, circumvent statutory disclosure obligations, or fill in internal evidence.

## License

This project is released under the **MIT License**. See [LICENSE](LICENSE). You may use, modify, distribute, and sell it freely, provided the copyright notice is retained.
