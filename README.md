# tango-product

An adversarial design loop for **new product ideas** — the product-mode companion to [tango-research](https://github.com/shivag/tango-research).

**The agent is the Driver**, proposing a PRD and technical plan for a product that doesn't exist yet. **Gemini is the Critic**, evaluating every proposal against three orthogonal rubrics — Product, Technical, Business — and flagging contradictions, scope creep, and undefended tech choices. **Every quantitative claim is grounded by a committed spike** — a prototype branch, user interview, competitor teardown, or unit-economics spreadsheet.

The output of a session is **three shipped documents** at the repo root:

- **`README.md`** — the ELI12 pitch, now stable enough to show someone
- **`PRD.md`** — user-facing spec, every user story traced to a rubric item
- **`TECH_PLAN.md`** — engineering spec, every architectural decision backed by a spike

Plus a full debate archive (`arena.md`, `rubric.md`, `debate_log.md`, `spikes/`) in `tango/<session>/` showing how the product got to its current shape.

---

## Install

```bash
npx skills add shivag/tango-product
```

Installs to `.agents/skills/tango-product/` (read by Cursor, Gemini CLI, Codex, Cline, Amp, OpenCode, and others) and symlinks into `.claude/skills/` for Claude Code.

---

## Prerequisites

- **Python 3** + `google-genai` — `pip install google-genai`
- **Gemini API key** at `~/.agents/tango.env` (preferred — scopes cleanly with [skills-watch](https://github.com/shivag/skills-watch) runtime guard) or `~/.agents/.env` (legacy shared-wallet fallback), as `GEMINI_API_TANGO_KEY=your-key` (or `GEMINI_API_KEY` as variable-name fallback). Get a key at [aistudio.google.com](https://aistudio.google.com/apikey).

If you already installed [tango-research](https://github.com/shivag/tango-research), the key is shared — no extra setup.

---

## How it works

Tango-product scaffolds a **new git repo** under `~/code/greenfield/<slug>/` and drives a debate loop inside it:

### Phase 0 — Bootstrap

Before touching the rubric, the Driver commits:
- **3–5 `spikes/competitor_*.md`** — teardowns of close competitors / substitutes
- **`spikes/icp.md`** — who the buyer is, what their pain is, what the wedge is
- **`spikes/niche_expansion.md`** — narrow-niche starting point + adjacent-niche expansion path

Then drafts a rubric with four constraint kinds:
- **`(mkt: segment/competitor)`** — market positioning floor
- **`(dep: platform/API)`** — technical dependency floor
- **`(biz: pricing/GTM)`** — unit-economics or go-to-market floor
- **`(inherent)`** — architectural premise; relaxing = different product

And **three orthogonal sub-rubrics**: Product (user-facing), Technical (engineering), Business (economic). Gemini attacks the rubric for vagueness, missing constraints, contradictions across sub-rubrics, and claims not supported by the spikes.

### Phase 1 — The Tango loop (up to 10 rounds)

Each round the Driver proposes a patch to `arena.md` (which has fixed sections: `# ELI12`, `# Product`, `# Technical`). If the patch contains a quantitative claim, a spike is committed alongside. Gemini checks the patch against:

- Every immutable constraint (violation = reject)
- Every item in all three rubrics
- Ungrounded claims (no spike / no citation)
- **Cross-section contradictions** (Product promise Technical can't deliver; Technical choice that breaks Business)
- Scope creep (features not traceable to any rubric item)
- Undefended tech choices ("why Postgres, not Dynamo?")

Every accepted patch updates the ELI12 summary so by convergence, the pitch is already written.

### Phase 1 — Constraint audit every 3 rounds

Every 3 rounds (or on any reject, or on demand), Gemini re-examines binding `(mkt/dep/biz)` constraints: "could we relax the JLC-min-feature floor by switching vendors?" is the manufacturing version; the product version is "could we relax the $50/mo ceiling by moving up-market?" If a relaxation would flip a rubric verdict, the loop STOPS and asks.

### Phase 2 — Converge: hoist into 3 shipped docs

When all three rubrics pass (or 10 rounds burn):
- `# ELI12` section → `README.md`
- `# Product` section → `PRD.md` with rubric traces and spike links
- `# Technical` section → `TECH_PLAN.md` with dependency floors and spike links

The debate archive stays in `tango/<session>/` for future sessions to inherit defeated rubrics.

---

## Usage

In any terminal:

> run tango-product: a SOC2-compliance copilot for solo SaaS founders

The agent will:

1. Ask whether this is a NEW product or a continuation of one of the existing `~/code/greenfield/*/` projects.
2. If new: scaffold the repo. If continuation: offer to inherit defeated rubrics from prior sessions.
3. Commit domain-research spikes (competitor teardowns, ICP, niche expansion).
4. Draft the three sub-rubrics and let Gemini attack them twice.
5. **Stop for your approval** before the Tango loop starts.
6. Run the loop, committing after every round, running a constraint audit every 3.
7. On convergence, hoist `arena.md` into `README.md`, `PRD.md`, `TECH_PLAN.md`.

Hard STOPs are built in — you're consulted at rubric approval, rubric level-up, deadlock, and any constraint-audit 🚨.

---

## Repo layout for your greenfield products

```
~/code/greenfield/
├── skills-security/              ← its own git repo
│   ├── README.md                 (ELI12 pitch, hoisted at converge)
│   ├── PRD.md                    (user-facing spec)
│   ├── TECH_PLAN.md              (engineering spec)
│   └── tango/
│       ├── 2026-04-18_mvp-shape/ (first session)
│       │   ├── rubric.md
│       │   ├── arena.md
│       │   ├── debate_log.md
│       │   └── spikes/
│       └── 2026-04-25_pivot/     (later pivot, inherits defeated rubrics)
└── skills-privacy/               ← separate product, separate repo
    └── tango/...
```

One product = one subdirectory = one git repo = many tango sessions over the product's life.

---

## Why this exists

Most "AI product-design assistants" produce confident-sounding PRDs full of unverifiable claims. Tango-product assumes adversarial critique by default: every quantitative number in your PRD or tech plan was challenged by a separate model with access to the full rubric, and defended with a committed artifact. When you hand someone a Tango-produced PRD, they can re-run the spikes and see the debate that produced every number.

The loop is structurally borrowed from Karpathy's [Autoresearch](https://x.com/karpathy/status/1937902205765607626) — applied to product design instead of research.

---

## Skill format

Follows the [agentskills.io](https://agentskills.io) spec — one `SKILL.md` per skill with YAML frontmatter:

```
tango-product/
└── skills/
    └── tango-product/
        └── SKILL.md
```

No manifest, no publish step. skills.sh indexes public GitHub repos directly.

---

## License

MIT. See [LICENSE](./LICENSE).
