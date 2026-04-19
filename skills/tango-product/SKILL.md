---
name: tango-product
description: Greenfield product-design loop. The agent acts as Driver proposing PRD + technical plan for a NEW product; Gemini acts as Critic evaluating every proposal against Product / Technical / Business rubrics; every quantitative claim is grounded by a committed artifact (prototype branch, interview transcript, competitor teardown, or unit-economics sheet). Use when the user is starting a new product idea from scratch — typically a new repo under ~/code/greenfield/ — and wants to grind vague ambition into a defensible PRD plus technical implementation plan before any production code is written. Produces README (ELI12 pitch), PRD (user-facing), and TECH_PLAN (engineering), all rubric-traceable and spike-backed.
---

# Tango Product Mode

You are executing **Tango Product Mode** — an adversarial design loop for a NEW product idea. You act as the **Driver** proposing the product's PRD + technical plan. Gemini acts as the **Critic** evaluating every proposal via API against three orthogonal rubrics (Product, Technical, Business). Every quantitative claim must be grounded by a committed **Spike** artifact (prototype, interview, teardown, or spreadsheet).

**The user's product idea** will be given in their request (for example, "a SOC2-compliance bot for solo SaaS founders"). If the idea is missing or unclear, ask for a 1-sentence pitch, then proceed.

The ultimate deliverables are **three documents** hoisted at Phase 2 from a single `arena.md`: `README.md` (ELI12 pitch), `PRD.md` (user-facing), `TECH_PLAN.md` (engineering). All rubric-traceable and spike-backed.

---

## Phase 0: Bootstrap

### Step 0: Pick the repo — new product or continuation?

Greenfield products live under `~/code/greenfield/<slug>/`. Each subdirectory is its own git repo; each repo can host multiple Tango sessions as the product evolves.

1. Run `ls ~/code/greenfield/ 2>/dev/null` and show the user the list.
2. Ask: **"Is this a NEW product idea or a continuation/pivot of one of these?"**
3. If **NEW**: ask the user for a kebab-case slug (e.g. `skills-security`). Then:
   ```bash
   mkdir -p ~/code/greenfield/<slug>
   cd ~/code/greenfield/<slug>
   git init
   echo "# <Product Name>\n\nGreenfield product — scaffolded by tango-product." > README.md
   echo ".DS_Store\n.env\n__pycache__/\n*.pyc" > .gitignore
   git add .
   git commit -m "Bootstrap: <slug> greenfield product"
   ```
4. If **CONTINUATION**: `cd` into the chosen subdirectory. List existing `tango/*/` sessions and ask whether to start a fresh session or resume one. **Fresh sessions can inherit defeated rubrics** from prior sessions — read any `tango/*/rubric.md` and copy their `🏁 Defeated Rubrics` into the new session's starting rubric so past debate isn't re-litigated.

Do NOT proceed to Step 1 until the user confirms the repo choice.

### Step 1: Create the session workspace

From inside the chosen product repo:

```bash
SESSION_NAME="$(date +%Y-%m-%d)_<short-slug-of-idea>"
mkdir -p "tango/$SESSION_NAME/spikes"
touch "tango/$SESSION_NAME/rubric.md"
touch "tango/$SESSION_NAME/arena.md"
touch "tango/$SESSION_NAME/debate_log.md"
```

### Step 2: Domain research (market-first — there are no physics floors here)

Greenfield products have no physics constraints. Market and business constraints are load-bearing instead. **Before drafting the rubric**, produce and commit these spikes:

1. **`spikes/competitor_<name>.md`** × 3 to 5 — for each close competitor or substitute:
   - What they do in one sentence
   - Who uses them (ICP)
   - Pricing
   - Where they're weak / what complaint themes show up in reviews (cite sources)
   - What a user would need to leave them for us

2. **`spikes/icp.md`** — one paragraph answering:
   - Who is the buyer (role, company stage, budget)?
   - What pain are they feeling today?
   - What do they do today to cope?
   - What's the wedge — the one thing we do that makes them switch?

3. **`spikes/niche_expansion.md`** — two paragraphs:
   - If we win the narrow niche, what adjacent niche unlocks next?
   - What would break if the narrow niche turns out to be a dead end (TAM too small, buyer doesn't exist, etc.)?

4. Web-search for 2–3 market-sizing data points relevant to the niche. Note them.

Commit these before drafting the rubric. These are evidence the rubric rests on.

### Step 3: Draft `rubric.md` — three orthogonal sub-rubrics

Write the initial rubric with this exact structure:

```markdown
# 🔒 Immutable Constraints
*Binary rules. Any proposal violating these is instantly rejected.
Every sub-item is tagged with its floor kind.*

- **C1.1 (mkt: <segment/competitor>)** <constraint> — <short justification>
- **C2.1 (dep: <platform/library/API>)** <constraint> — <short justification>
- **C3.1 (biz: <pricing/GTM>)** <constraint> — <short justification>
- **C4.1 (inherent)** <constraint> — <short justification>

# 🎯 Product Rubric (Level 1)
*User-facing binary pass/fail. What must be true for the user to care?*
- [ ] <specific, measurable, falsifiable user-visible goal>

# 🎯 Technical Rubric (Level 1)
*Engineering binary pass/fail. What must be true for the system to actually work?*
- [ ] <specific, measurable, falsifiable performance/correctness goal>

# 🎯 Business Rubric (Level 1)
*Economic binary pass/fail. What must be true for the business to work?*
- [ ] <specific, measurable, falsifiable unit-economics / GTM goal>

# ✅ Done Criteria
- All three current rubrics pass AND user approves final arena.md.

---
## 🏁 Defeated Rubrics
*(empty, or: copy from prior session's defeated rubrics if continuation)*
```

Rules:
- Every rubric item must be **binary** (pass/fail) and **verifiable** (by spike or citation). No 0-10 scales. No vague words like "easy," "delightful," or "scalable."
- **Every rubric MUST include at least three "antibiotic" items** — painkillers, not vitamins — at least one each:
  - **Painkiller test** (Product rubric): the target user is suffering a *named, quantified* pain today; shipping the MVP removes it. A vitamin is nice-to-have; if the user's life is fine without this product, flag it. Example antibiotic: "solo devs currently spend ≥ 30 min manually auditing each new skill; MVP reduces to < 30 seconds." Example vitamin (reject): "improves developer experience."
  - **Friction floor** (Product rubric): *time from install to first-delivered-value is ≤ N minutes, measured by a new-user walkthrough*. No account creation, no config files, no reading docs. If the MVP needs any of those, quantify the friction and defend it.
  - **Time-to-user** (Business rubric): *v0 ships in ≤ N days from rubric approval, usable by a single real developer who isn't you*. A product nobody touches for 3 months while it gets perfect isn't an antibiotic — it's a research project.
- Every **constraint sub-item** must carry one of four floor tags:
  - **`(mkt: <segment/competitor>)`** — market/positioning floor. Relaxable only by changing who the product is for. (e.g. `(mkt: solo SaaS founders)` buyer budget ≤ $100/mo.)
  - **`(dep: <platform/library/API>)`** — technical dependency floor. Relaxable only by swapping the dependency. (e.g. `(dep: Cloudflare Workers)` 30s CPU cap per request.)
  - **`(biz: <pricing/GTM>)`** — unit-economics or go-to-market floor. Relaxable only by changing pricing/channel. (e.g. `(biz: prosumer SaaS 2026)` can't charge > $50/mo without enterprise sales motion.)
  - **`(inherent)`** — architectural premise of the product. Relaxing = redesigning the product. (e.g. `(inherent)` all auditable artifacts are user-owned, not platform-owned.)

The tag is load-bearing for the constraint audit in Phase 1 Step H — without it, the audit can't distinguish a hard wall from a soft choice.

### Step 4: Send rubric to Gemini for attack

**API key loading.** The user stores secrets in `~/.agents/` as `KEY=value` lines. Tango-product shares the `GEMINI_API_TANGO_KEY` key with tango-research. Always check **`~/.agents/tango.env` first** (preferred — scopes cleanly with [skills-watch](https://github.com/shivag/skills-watch) or any other runtime guard, so only Tango-family skills can read the Tango Gemini key), then fall back to **`~/.agents/.env`** (legacy shared-wallet location). Load `GEMINI_API_TANGO_KEY` first, fall back to `GEMINI_API_KEY`, then the process env. Do NOT trust a key already in `os.environ` unless it matches a file value — placeholder values like `your-key` are common.

Write and execute this Python script:

```python
#!/usr/bin/env python3
import os, sys, pathlib
from google import genai

def load_key():
    # Preferred: ~/.agents/tango.env (scopes with skills-watch / per-skill allow-lists).
    # Fallback: ~/.agents/.env (legacy shared-wallet location).
    env_files = [
        pathlib.Path.home() / ".agents" / "tango.env",
        pathlib.Path.home() / ".agents" / ".env",
    ]
    file_vals = {}
    for env_file in env_files:
        if env_file.exists():
            for line in env_file.read_text().splitlines():
                line = line.strip()
                for name in ("GEMINI_API_TANGO_KEY", "GEMINI_API_KEY"):
                    if line.startswith(f"{name}=") and name not in file_vals:
                        file_vals[name] = line.split("=", 1)[1].strip().strip('"').strip("'")
    return (file_vals.get("GEMINI_API_TANGO_KEY")
            or file_vals.get("GEMINI_API_KEY")
            or os.environ.get("GEMINI_API_TANGO_KEY")
            or os.environ.get("GEMINI_API_KEY"))

api_key = load_key()
if not api_key or api_key in ("your-key", ""):
    print("ERROR: GEMINI_API_TANGO_KEY / GEMINI_API_KEY not found in ~/.agents/tango.env, ~/.agents/.env, or environment"); sys.exit(1)

client = genai.Client(api_key=api_key)

rubric = open("tango/<SESSION>/rubric.md").read()

# Read the spikes committed in Step 2 — these are the rubric's evidence base.
spikes = ""
spikes_dir = pathlib.Path("tango/<SESSION>/spikes")
for f in sorted(spikes_dir.glob("*.md")):
    spikes += f"\n\n--- {f} ---\n" + f.read_text()

response = client.models.generate_content(
    model="gemini-2.5-pro",
    contents=f"""You are the CRITIC in a Tango Product session. ATTACK this rubric for vagueness, unmeasurability, missing constraints, or claims that aren't supported by the committed spikes.

SPIKES (competitor teardowns, ICP, niche expansion):
{spikes}

PROPOSED RUBRIC:
{rubric}

Tasks:
1. Find any item that is vague or unmeasurable. Propose a quantified, binary replacement.
2. Find MISSING constraints the product obviously needs — particularly (mkt) and (biz) floors the spikes imply.
3. Find any item across the three sub-rubrics (Product / Technical / Business) that CONTRADICTS another. Flag them.
4. Find any rubric item too easy to pass (trivial win) and propose a harder version.
5. Check every constraint against the spikes — is there evidence for this being a floor, or is it asserted?
6. **Antibiotic check** — name every rubric item that reads like a vitamin ("improves X," "enhances Y," "enables Z") and either delete it or rewrite as a painkiller tied to a named, quantified user pain. Vitamins are easy to ship and nobody cares; antibiotics are hard but someone pays immediately.
7. **Friction audit** — for every Product-rubric item, ask "what does the user have to do *before* they get value?" If the answer is more than one step (install + one command), flag the item and propose a simpler version that hides or eliminates the step.
8. **Simpler-more-powerful sweep** — for every Technical-rubric or architecture claim, ask "is there a dumber design that delivers 80% of the value in 20% of the build?" If yes, name it. Prefer one-file scripts over frameworks, prefer hooks on existing tools over new daemons, prefer a shell wrapper over a TypeScript service.
9. Output your COMPLETE revised rubric in the exact same markdown format.

Be ruthless. Every vague word you let through will cause a product to ship that doesn't work. Every vitamin you let through will cause a product to ship that nobody uses."""
)
print(response.text)
```

### Step 5: Integrate Gemini's feedback

Revise `rubric.md` with valid critiques. Note disagreements in `debate_log.md`. Repeat the Gemini call once more (2 total rounds of rubric debate).

### Step 6: Seed the arena with three sections + ELI12

`arena.md` has a fixed top-level structure during debate:

```markdown
# ELI12
<1–2 paragraph plain-English pitch — what the product IS and why a user cares.
At bootstrap this is the opening premise; every ACCEPT updates it.>

# Product
<User-facing: who it's for, what it does, key user stories, wedge.
Each claim cites a rubric item (R1.1, R1.2, ...) and/or a spike.>

# Technical
<Engineering: architecture, key dependencies, data model, major risks.
Each claim cites a rubric item and/or a spike.>

# ELI12 Changelog
| Version | After Round | What's new since previous | Why it changed |
|---|---|---|---|
| v0 | bootstrap | Initial ELI12 written. | Session start. |
```

Every ACCEPT verdict in Phase 1 triggers a mandatory ELI12 update: rewrite the top ELI12 paragraph and add a changelog row describing the delta in user-facing language (what a 12-year-old would notice changed, NOT the feature name or endpoint). By convergence, the ELI12 IS the pitch.

### Step 7: Git commit + present to user

```bash
git add tango/<SESSION>/
git commit -m "tango: init session '<SESSION>'"
```

**STOP HERE.** Present the rubric (all three sub-rubrics + constraints) to the user. Explain each item. Ask if they want changes. Do NOT proceed until the user explicitly says to continue.

After approval:
```bash
git add tango/<SESSION>/rubric.md
git commit -m "tango: rubric approved (level 1)"
git push 2>/dev/null || true   # remote may not exist yet — that's fine
```

---

## Phase 1: Tango Loop (max 10 rounds)

For each round:

### A. Driver (you) proposes a patch

1. Read `rubric.md`, `arena.md`, `debate_log.md`.
2. Find the weakest section in `arena.md` (Product or Technical) relative to any of the three rubrics.
3. Write your proposed change targeting a specific section and naming which rubric item(s) it addresses.
4. If the proposal has a quantitative claim, write a Spike (Step B).

### B. Spike (if needed)

Spikes in tango-product are NOT just Python. They are any committed artifact that grounds a claim:

- **`spikes/perf_<topic>.py`** + `spikes/perf_<topic>_output.txt` — a load test, latency measurement, or cost calculation. Python, run under 60s timeout:
  ```bash
  timeout 60 python3 "tango/<SESSION>/spikes/perf_<topic>.py" > "tango/<SESSION>/spikes/perf_<topic>_output.txt" 2>&1
  ```
- **`spikes/prototype_<topic>/`** — a throwaway prototype subdirectory. README inside explains what ran, what output it produced, what the claim is.
- **`spikes/interview_<name>.md`** — a transcribed or summarized user interview. Must include: who, what they said verbatim (3+ quotes), what the inferred rubric implication is.
- **`spikes/competitor_<name>.md`** — follow Phase 0 Step 2's template.
- **`spikes/unit_economics.csv`** + **`spikes/unit_economics_notes.md`** — a spreadsheet of cost-per-user, cost-per-call, revenue-per-user, with written assumptions.
- **`spikes/pricing_<variant>.md`** — a pricing-hypothesis doc: what plan, what tier limits, what the implied conversion funnel is, what the math yields.

The rule: **every quantitative claim in `arena.md` must point to a spike committed in the same session.** No spike = unsupported.

### C. Send to Gemini for critique

Execute a Python script that sends the rubric, arena, debate log, your proposed patch, and any spike output to Gemini with this prompt:

```
You are the CRITIC in a Tango Product session.

RUBRIC: {rubric}
CURRENT ARENA: {arena}
DEBATE LOG: {debate_log}
PROPOSED PATCH: {patch}
SPIKE OUTPUT / ARTIFACTS: {spike_output_or_paths}

Tasks:
1. Check patch against every 🔒 Immutable Constraint. Violation → REJECT.
2. Check against every item in the Product / Technical / Business rubrics. State PASS or FAIL with reason for each.
3. Flag any UNGROUNDED claims (no spike, no citation, no rubric trace).
4. Flag any CROSS-SECTION CONTRADICTIONS — a Product promise the Technical section can't deliver, a Technical choice that breaks a Business constraint, etc.
5. Flag any SCOPE CREEP — a feature in the patch that doesn't map to any rubric item.
6. Flag any TECH CHOICE without defense — "why Postgres, not Dynamo?", "why Next, not Remix?"
7. **VITAMIN FLAG** — is this patch adding a "nice-to-have" instead of addressing a named quantified pain? If yes, REJECT and name the vitamin. Acceptable patches tighten painkillers; patches that add polish before the painkiller lands are rejected.
8. **FRICTION FLAG** — does this patch add a step between "user installs" and "user gets value"? Count the steps in the happy path (install + commands + config + account signup + ...). If the patch grows that number, REJECT unless the gain justifies the step.
9. **SIMPLER-MORE-POWERFUL CHALLENGE** — propose at least one dumber design that delivers ≥ 80% of the patch's value in ≤ 20% of the build. One-file script > framework. Hook on existing tool > new daemon. Shell wrapper > full service. If the simpler design exists, REJECT and name it; the Driver has to defend why the fancier design is needed.
10. **TIME-TO-USER FLAG** — would this patch delay v0 reaching a real outside developer by more than a few days? If yes, REJECT unless the patch is on the critical path to shipping. Polish, abstractions, and future-proofing get deferred until after v0 is in somebody's hands.

Output format:
VERDICT: ACCEPT or REJECT
CONSTRAINT CHECK: (each constraint, PASS/FAIL)
PRODUCT RUBRIC CHECK: (each item, PASS/FAIL)
TECHNICAL RUBRIC CHECK: (each item, PASS/FAIL)
BUSINESS RUBRIC CHECK: (each item, PASS/FAIL)
UNGROUNDED CLAIMS: (list)
CROSS-SECTION CONTRADICTIONS: (list)
SCOPE CREEP: (list)
UNDEFENDED TECH CHOICES: (list)
VITAMINS: (list — patch elements that are nice-to-have, not painkiller)
FRICTION ADDED: (list — new steps between install and first value)
SIMPLER ALTERNATIVES: (list — dumber designs that get 80% of the value)
TIME-TO-USER RISK: (list — anything that delays v0 reaching a real outside user)
REASONING: (2-3 sentences)
SUGGESTION: (if rejecting, what to change)

If ALL items PASS across all three rubrics:
RUBRIC EVOLUTION: (propose harder Level N+1 items per sub-rubric)
```

### D. Process verdict

- **ACCEPT:** Apply patch to `arena.md`. **Also rewrite the ELI12 at the top to reflect the new state, and add a row to the ELI12 Changelog** describing the user-facing delta. Update `debate_log.md`.
- **REJECT:** Don't touch `arena.md` or the ELI12. Log rejection in `debate_log.md`. Increment reject counter.

### E. Deadlock check

3 consecutive rejects on the same item → **run the constraint audit (Step H) immediately**, then STOP and present to the user: "DEADLOCK on [item]. Constraint audit says [constraint] has a (mkt/dep/biz) floor of X; current rubric value is Y. Relaxing to X would [impact]. Which should we relax?"

### F. Rubric evolution

If Gemini says all items pass and proposes Level N+1:
1. Move current items in the relevant sub-rubric(s) to `🏁 Defeated Rubrics`.
2. Write new items to the appropriate `🎯 ... Rubric (Level N+1)` section.
3. **STOP.** Show user the proposed new level for approval.

### G. Git commit

```bash
git add tango/<SESSION>/
git commit -m "tango: round N - <summary>"
```

### H. Constraint audit — prevent over-constraint

**Triggers (any one fires):** every 3 rounds (3, 6, 9), any REJECT verdict, any R-item FAIL, or user request ("run a constraint audit" / "are we over-constrained?").

**How it works — filter by tag:**

1. **Skip** every `(inherent)` constraint. Relaxing it means redesigning the product — that's a separate conversation.
2. **For each `(mkt: <segment>)` tagged constraint** that is BINDING (current design sits at the limit) or NEAR (within 1.5× of the limit): ask Gemini what market-positioning change would relax it (different ICP, different competitor, different segment maturity) and quantify the design-space impact.
3. **For each `(dep: <platform>)` tagged constraint** that is BINDING or NEAR: ask Gemini to name an alternative platform/library with a looser floor, quantify the impact on the Technical rubric.
4. **For each `(biz: <pricing/GTM>)` tagged constraint** that is BINDING or NEAR: ask Gemini what pricing-tier or GTM-motion change would relax it, quantify the impact on the Business rubric.

**Gemini prompt (short):**
```
For each of these tagged constraints, the design is BINDING or NEAR the limit.

  [paste the subset of (mkt/dep/biz) sub-items from rubric.md alongside
   the spike-measured design values that bump against them]

For each:
  - mkt tags: name one segment/competitor shift that relaxes the floor, OR say "not relaxable without abandoning the niche".
  - dep tags: name one alternative platform/library with a published looser floor, OR say "no known alternative".
  - biz tags: name one pricing or GTM change that relaxes it, OR say "not relaxable without changing the business model".
  - Quantify the design-space impact: which R-item in which sub-rubric moves, by how much, and whether a verdict flips.

If any relaxation would flip an R-item verdict (FAIL → PASS, or PASS → FAIL), prefix with 🚨.
Output: one row per relaxable sub-item, then a prose summary of the 🚨 flags.
```

**Save output to:** `constraint_audit_rN.md` alongside `arena.md`.

**STOP the loop and present to the user** if any 🚨 flag appears. Frame as:

> "C_x.y is currently Y, tagged (mkt/dep/biz: Z). Relaxing to <new value> via <alternative> would flip R_k from FAIL → PASS (metric: X vs threshold T). Do you want to relax?"

**Commit:** `git add constraint_audit_rN.md && git commit -m "tango: constraint audit round N"`.

### H.1 Post-pivot hygiene — grep before the next critique

If the audit triggers a rubric or constraint pivot (user approves a relaxation that flips an R-item verdict), the **next Driver patch MUST include a pre-critique grep sweep for pre-pivot vocabulary.**

Stale terms in `arena.md`, `rubric.md`, `arena.md`'s ELI12, and any patch proposal that survive a surgical rewrite create cross-section contradictions the Critic will flag as fatal. These rejections are *preventable*.

**Mechanic.** Before sending the post-pivot patch to the Critic:

1. Identify the vocabulary the pivot retired. Example: if `C2.2` flipped from *CLI wrapper* to *agent hook*, retired terms include `npx skills-watch <skill>`, `--allow <path>` CLI flag, env-var-based overrides, any example output referencing the old mechanism.
2. `grep -rn "<term>"` across `arena.md`, `rubric.md`, any open patch files, and the ELI12 changelog.
3. Normalize every occurrence to the new vocabulary.
4. Only then invoke the Critic.

**Why this matters.** Without the sweep, a single pivot costs two Critic rounds instead of one — one for the structural change, a second REJECT for the stale references the structural change missed. Two rounds of Gemini latency + user wait. The grep sweep takes seconds and saves a round.

**Verification.** The Driver should paste the grep output (showing zero matches for the retired terms) into `debate_log.md` before sending the patch, so the Critic can trust the sweep happened.

---

## Phase 2: Converge — hoist arena.md into three shipped documents

When done criteria met (all three rubrics PASS) or 10 rounds exhausted:

### Step 1: Hoist `arena.md` into the final deliverables

The product repo's root gets three new files, each derived from `arena.md`:

1. **`README.md`** — rewrite the repo-root README. Top of file is the final ELI12 pitch from `arena.md`. Below it, add an "Install / Run" section if applicable, and a "See also" section linking to `PRD.md` and `TECH_PLAN.md`.

2. **`PRD.md`** — hoist the `# Product` section of `arena.md` verbatim. Add at the top a "Rubric trace" — every user story cites the R-item(s) that justify it. Add at the bottom a "Evidence" section linking to the spikes that grounded each claim.

3. **`TECH_PLAN.md`** — hoist the `# Technical` section. Same structure: rubric trace at top, spike evidence at bottom. Add a "Dependency floors" section listing every `(dep: X)` constraint and its concrete limit, so engineers know the walls.

Leave `tango/<SESSION>/arena.md` intact — it's the debate archive. The three root files are the forward-facing deliverables.

### Step 2: Final summary + commit

1. Add `## Final Summary` to `debate_log.md` (rounds, levels achieved, accepted/rejected totals, spikes committed, key pivots).
2. Commit:
   ```bash
   git add README.md PRD.md TECH_PLAN.md tango/<SESSION>/
   git commit -m "tango: session '<SESSION>' complete — PRD + TECH_PLAN hoisted"
   ```
3. If a remote exists (`git remote -v` is non-empty): `git push`. If not, tell the user to run `gh repo create` when ready to publish.

### Step 3: Tell the user what shipped

Report:
- **Product deliverables:** `README.md`, `PRD.md`, `TECH_PLAN.md` (root-level, forward-facing)
- **Debate archive:** `tango/<SESSION>/arena.md`, `rubric.md`, `debate_log.md`, `spikes/`
- **Next session:** if they want to pivot or evolve, re-run `tango-product` from this same repo — new sessions can inherit defeated rubrics so prior debate isn't re-litigated.
