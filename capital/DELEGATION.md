# Delegation Spec — El Doce Capital System

Routes every piece of the capital-raising system (see `RAISE-SYSTEM.md`) to the
right Claude model tier. Principle: **the frontier tier owns structure and
anything compliance-sensitive; drafting and sequencing push down the ladder.**
Cheap tiers assemble; they never author claims.

## The ladder

| Tier | Model | ID | Price (in/out per MTok) | Owns |
|---|---|---|---|---|
| **Frontier** | Claude Opus 4.8 | `claude-opus-4-8` | $5 / $25 | Structure, compliance framing, high-stakes persuasion |
| **Mid** | Claude Sonnet 5 | `claude-sonnet-5` | $3 / $15 (intro $2 / $10 through 2026-08-31) | Content drafting inside approved frames |
| **Fast** | Claude Haiku 4.5 | `claude-haiku-4-5` | $1 / $5 | Sequencing, personalization, CRM ops |

---

## Tier 1 — Frontier (Opus 4.8): structure & compliance

Low volume, high stakes. Runs occasionally; its outputs become the frames every
other tier fills.

- **The Approved Language Library** — authorship and every revision. All rate,
  return, security, risk, and testimonial language originates here, then goes to
  the attorney once, then is frozen.
- **Funnel architecture** — the stages, scripts, and scoring model in
  RAISE-SYSTEM.md; quarterly review against the metrics.
- **The pitch** — deck narrative, one-pager copy, discovery and pitch-meeting
  scripts, the CPA one-pager.
- **Objection handling** — the objection matrix, plus every *novel* objection
  that comes in from the field (logged by the fast tier, answered here, added
  to the matrix).
- **Anything investor-legal-adjacent** — welcome-kit copy, quarterly-letter
  template, any content that states numbers, terms, or performance.
- **Escalations** — media inquiries, complaint drafts, anything where a wrong
  sentence has legal or reputational cost.

Settings: adaptive thinking (default), `effort: "high"`; `"xhigh"` for the
quarterly strategy review. Human gates: Joe approves everything investor-facing;
the attorney approves the library and legal-adjacent templates once per
revision.

## Tier 2 — Mid (Sonnet 5): content drafting

Weekly volume. Always drafts **inside a frontier-authored frame** — an approved
outline, the brand voice guide (`brand/BRAND.md`), and the Approved Language
Library are in every prompt.

- Education ladder content: lead magnet, the 5-lesson mini-course, Lunch &
  Learn script and slides, monthly deal-teardown drafts
- The 52-week prospect drip (drafted in monthly batches of 4–5)
- Quarterly investor letter drafts (numbers and claims pasted verbatim from the
  library and the Proof File — flagged for frontier review only if a new kind
  of claim appears)
- Lonchando social/content pillar drafts (LA MENTE, LA ESCALERA, EL RITMO, LA
  MESA posts)

Hard rule: **Sonnet never writes a new sentence about returns, terms, or
performance.** Where one is needed, it inserts the library snippet verbatim or
leaves a `[FRONTIER: needs approved language]` marker.

## Tier 3 — Fast (Haiku 4.5): sequencing & relationship ops

Daily volume, small tokens, high count. This tier is what carries the
relationship follow-through (RAISE-SYSTEM.md Stage 6).

- Monthly payment-confirmation notes (template + one CRM fact merged in)
- Birthday / note-anniversary messages drafted from CRM notes
- Annual-review call briefs (one page from the contact's CRM history)
- The Monday Relationship Hour queue: assemble approvals, calls, and the
  90-days-quiet list
- CRM hygiene: tag new contacts, score against the 4-criteria profile, set
  next-touch dates, log meeting-note summaries
- Novel-objection logging (verbatim capture + route to frontier)

Hard rules: assembles from templates and CRM facts only; **never generates
numbers, rates, or promises**; anything off-template escalates one tier up.

---

## Routing rules (apply to every job)

1. **Claims lock.** Any output mentioning rate, return, term, security, or
   performance must use Approved Language Library snippets byte-for-byte.
   Lower tiers assemble; only the frontier tier authors; only the attorney
   approves new library entries.
2. **Escalate on novelty.** New objection, new claim type, new channel, new
   audience → one tier up, automatically. When in doubt, up.
3. **Human gate.** Nothing reaches an investor or prospect without Joe's
   approval (the Monday queue). The machine drafts; Joe sends.
4. **Frames flow down, never up.** Frontier writes frames → mid fills frames →
   fast merges facts into filled frames. A lower tier never modifies a frame.

## Implementation notes

- **Prompt caching:** put the stable system prompt (brand voice + Approved
  Language Library + compliance rules) first with `cache_control: {"type":
  "ephemeral"}` — every drafting call reuses it at ~0.1× input price. Keep it
  byte-stable; volatile content (the week's CRM facts) goes last.
- **Batch API for the weekly runs:** the Monday queue and monthly drip batches
  are not latency-sensitive — run them through the Message Batches API for 50%
  off all token usage.
- **Structured outputs for CRM ops:** Haiku jobs that write into the CRM
  (scores, tags, next-touch dates) use `output_config.format` with a JSON
  schema so records are machine-valid every time.

### Cost picture (order of magnitude)

The whole system is light: the frontier tier runs on a handful of documents per
quarter; the mid tier a few dozen drafts per month; the fast tier hundreds of
small merges. At current pricing this is tens of dollars per month, dominated by
the mid tier — with caching and batching, likely less. The constraint is Joe's
Monday hour, not the model bill.
