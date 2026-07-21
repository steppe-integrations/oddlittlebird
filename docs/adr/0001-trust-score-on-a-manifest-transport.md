# ADR-0001 — Trust scoring on a manifest transport, over four alternatives

**Status:** Accepted (2026-07-20) · **Review by:** after Session 2 produces implementation evidence

## Context

The collaboration needed to choose what its technical contribution *is*: given an
upstream standard (artifact-driven development, practiced in
`djcdevelopment/commandcenter`) and a downstream standard (a four-gate verification
pipeline, practiced in `oddlittlebird/showcase-site`), what cohesive implementation do
the two combine into? The objective: show how the standards enhance each other — and
how pieces of them provide better total coverage when enterprise adoption blockers
remove parts of the whole.

Five architectures were generated and scored (cohesion / adoptability-in-pieces /
demo-ability in 90 minutes / how hard it forces the upstream standard to formalize):

| Architecture | Cohesion | Pieces | Demo | Formalize |
|---|---|---|---|---|
| Provenance Spine (12th artifact type, one graph) | 9 | 5 | 4 | 9 |
| Seam Contract (AXF manifest only) | 5 | 9 | 8 | 6 |
| Coverage / Failure-Mode Ledger | 6 | 10 | 9 | 7 |
| Maturity Ladder (tiers 0–3) | 4 | 10 | 7 | 8 |
| **Trust Score (composite per page)** | **8** | **7** | **9** | **7** |

## Decision

Adopt the **Trust Score** as the core architecture, carried on an **AXF-style manifest**
as its transport. The other three candidates are absorbed, not discarded:

- the **manifest** becomes `/manifest` — the only object crossing an org boundary;
- the **coverage ledger** becomes `/coverage` — the governance artifact that sells the
  rollout before code exists;
- the **maturity ladder** becomes the `ceiling` vocabulary inside `/adoption-profiles` —
  degradation lands on a named tier, never on "half done";
- the **provenance spine** is deferred: its ambition survives as the `inputs[]` audit
  trail on every trust record, without requiring either toolchain to rebuild.

Spec: [`spec/trust-api.yaml`](../../spec/trust-api.yaml).

## Rationale

Trust scoring is the only candidate where each standard's admitted weakness is repaired
by the other's strength in a single move. The downstream standard has no provenance
model — the artifact graph becomes its provenance input. The upstream standard is
implicit and timestamp-organized — feeding a computable score forces its signals
(status headers, ADR references, ledger digests) into a schema. Neither repair works
without the other party.

Decisive evidence, found during the survey: both repos already contain the same idea
built from opposite ends. `docs/doc-claims.json` (commandcenter) machine-checks prose
claims against system state; Doc Detective (showcase-site) machine-checks documented
steps against the live product. The spec models them as one gate (`accuracy`) with two
targets. A contribution that *names what both parties already built* beats one that
asks either to adopt something foreign.

## Consequences

- The weighting formula is deliberately not in the spec; every implementation must
  record its weights as an ADR. The upstream discipline becomes structurally required
  downstream.
- Two observed status vocabularies (`Accepted/Implemented/Pending`,
  `TODO/DONE/BLOCKED/GATED`) unify into one lifecycle enum with `superseded` as a
  status, never a deletion. This forces the upstream standard to standardize — an
  acknowledged cost, accepted because it was already the owner's stated intent.
- A single scalar can be gamed or misread. Mitigation: `inputs[]` is required — a score
  that cannot be audited back to its artifacts and verification records is
  nonconforming.
- Public proof of collaboration remains absent until a working session produces a merged,
  gate-passing change on a public repository. This ADR picks an architecture; it does not
  manufacture proof.
