# AGENTS.md

Guide for AI assistants and automated readers working with this repository.
Human readers should start at [`README.md`](README.md), which is short and
points at the sources and their identifiers. This file is the most detailed
guide in the repository.

This repository is **not a software project**. It is a research paper plus its
machine-checked formal development. There is no application to run, no test
suite to extend, and no feature work. Your likely task is to *read, explain,
cite, or check*. This file therefore gives you the order of authority among the
sources, the vocabulary, the theorem index, and the claims this work does
**not** make.

**Links.** Paper: [arXiv:2605.31475](https://arxiv.org/abs/2605.31475)
([v5 abs](https://arxiv.org/abs/2605.31475v5) ·
[v5 PDF](https://arxiv.org/pdf/2605.31475v5) ·
[in-repo PDF](paper/dblog_virtual_cuts_v5.pdf) ·
[sources](paper/)). Formal development:
[`formal/`](formal/) · archived at Zenodo
[10.5281/zenodo.21732790](https://doi.org/10.5281/zenodo.21732790) (version 2.1)
· [10.5281/zenodo.20389696](https://doi.org/10.5281/zenodo.20389696) (concept
DOI). Derived reading: [`docs/THEOREMS.md`](docs/THEOREMS.md) ·
[`docs/PROVENANCE.md`](docs/PROVENANCE.md) ·
[`formal/README.md`](formal/README.md). Prior work:
[2020 DBLog paper](https://arxiv.org/abs/2010.12597) ·
[2019 Netflix Tech Blog post](https://netflixtechblog.com/dblog-a-generic-change-data-capture-framework-69351fb9099b).
Author: Andreas Andreakis,
[ORCID 0009-0003-9025-9402](https://orcid.org/0009-0003-9025-9402).

---

## 1. What this is, in one paragraph

The paper **“A Theoretical Study of DBLog: Certified Virtual Cuts for a
Snapshot-Equivalent Replay of Live Databases”** (Andreas Andreakis, 2026,
[arXiv:2605.31475](https://arxiv.org/abs/2605.31475)) formalizes the DBLog
change-data-capture backfill mechanism. Its central object is the **certified
virtual cut**, a finite bundle of CDC events and chunk reads whose replay
reaches the same per-key state as the source at a chosen frontier on a chosen
key scope. Nine main theorems are machine-checked in Isabelle/HOL under the
assumptions stated in them. The development's other theorems are closed
witnesses and fixtures. They exercise the definitions and show that the
theorems are not vacuous.

## 2. Source precedence

When sources disagree, the higher entry wins. If you find a disagreement,
report it. It is a defect.

1. **`formal/`**: the Isabelle/HOL sources. They are kernel-checked and decide
   what is proved and under which assumptions.
2. **`paper/main.tex`** and the arXiv PDF: the paper of record, including its
   scope statements, non-claims, and deployment obligations.
3. **`formal/README.md`**: the artifact's own description of its contents.
4. **`README.md`, `docs/`, this file**: derived explanation for readers. They
   are useful, but entries 1–3 take precedence over them.

Never infer a theorem's content from prose in this repository. Read the
statement in `formal/`.

## 3. Repository map

| Path | Contents | Authoritative? | Editable? |
|---|---|---|---|
| `formal/*.thy`, `formal/ROOT`, `formal/document/` | Isabelle/HOL session `DBLog_Virtual_Cuts`, 38 theories | yes, for proofs | **no, byte-frozen** |
| `formal/README.md` | artifact README (theory-by-theory) | yes, for artifact description | no |
| `paper/main.tex`, `paper/refs.bib`, `paper/figures/` | arXiv v5 sources | yes, for the paper | only by the author |
| `paper/*.pdf` | built paper | yes | no |
| `docs/THEOREMS.md` | verbatim theorem statements, assumptions, non-claims | no (derived) | yes, by the author |
| `docs/PROVENANCE.md` | paper/artifact version history and DOIs | no (derived) | yes, by the author |
| `README.md`, `AGENTS.md`, `CITATION.cff` | hub pages | no (derived) | yes, by the author |

## 4. Hard rules

1. **Do not edit anything under `formal/`.** Those bytes are identical to the
   archived Zenodo deposit (version 2.1, DOI `10.5281/zenodo.21732790`). An edit
   breaks the correspondence between this repository and the archival record,
   and nothing in the repository would show it. If you believe you have found a
   defect, report it as an issue with a reproduction. Artifact defects are fixed
   by publishing a new version. They are not fixed by patching this tree.
2. **Never state a theorem without its assumptions.** Each of the nine main
   theorems holds only under its stated assumptions. This work does not claim
   “DBLog is correct”. It does claim “under the wellformed-run assumptions, the
   clean prefix of a DBLog run replays to the source state at its frontier on
   its scope”. Use the paper's status label **Machine-checked**. The label
   `Conditional` is retired.
3. **Never claim the work verifies an implementation.** It models a mechanism.
   Debezium, Apache Flink CDC, and Netflix's DBLog implement that mechanism.
   None of them is verified here.
4. **Preserve the deployment/observation split.** The verifier's `Accept` does
   not establish faithful source observation, and no theorem discharges the
   deployment obligations. See §7.
5. **Do not run `isabelle build` unless the user asked for a build.** A full
   check takes minutes and needs Isabelle2025-2 plus a LaTeX toolchain. Read
   the sources instead.
6. **Cite exact identifiers.** Use the version DOI when you refer to specific
   files, and the concept DOI when you mean “the artifact”. See §9.

## 5. Vocabulary (use these words as defined here)

| Term | Meaning here |
|---|---|
| **source history** | The append-only sequence of source events. Positions are *source coordinates*. |
| **frontier** | A position in the source event order at which a claim is made. The real-world analogue is an LSN or CDC watermark. |
| **scope** (`K`) | The set of keys a claim covers, e.g. one table's primary keys. Claims never extend past it. |
| **chunk** | A primary-key range read from the table. Its rows enter the stream as *refresh events*. |
| **CDC event** | A change event from the source log. A later CDC event dominates a stale refresh event for the same key. |
| **clean prefix** | The canonical replayable prefix constructed from a run: refresh and CDC events merged in source-coordinate order. |
| **virtual cut** | The property that replaying a bundle reaches the source state at a frontier on a scope. It is *extensional*: it states an equality of outcomes. It does not describe a physical snapshot read, and it asserts no single source timestamp across chunk rows. |
| **certificate** | The evidence object carrying scope, frontier and clean prefix. |
| **evidence** | The carrier recording the run backing a certificate. |
| **verify** | The three-way checker: `Accept`, `Reject`, `Unsupported`. |
| **wellformed run** (`WF`) | The run-model obligations: log retention, frontier discipline, watermark consistency, chunk-read fidelity. |
| **faithful source observation** (`FSO`) | The assumption that the observation the checker consumes reflects the real source. It is external and cannot be checked from the certificate. |
| **deployment obligations** | The conditions a real deployment must establish (faithful CDC delivery, watermark placement, retention). They are assumptions of the theorems. The proofs do not establish them. |
| **anchor domain / whole-table scope** | The Layer 4 machinery specializing a claim to an entire table. |

**Paper wording.** arXiv v5 writes *accurate source observation* for `FSO`.
arXiv v4 wrote *faithful source observation*. The symbol `FSO` is unchanged, and
the Isabelle sources keep the identifier `faithful_source_observation`. This
file and `docs/` keep *faithful source observation*, which matches the Isabelle
identifier.

Avoid: “snapshot” without qualification (there is no physical snapshot),
“guarantees”, “ensures exactly-once”, “proves DBLog correct”.

## 6. Theorem index

Nine main theorems. Locations are `formal/<file>:<line>` in the frozen tree.
Several also exist in locale form. The locale copy is the abstract statement,
and the listed location is the public one.

### Core ladder

| # | Theorem | Location | Says |
|---|---|---|---|
| 1 | `wellformed_run_implies_virtual_cut` | `Virtual_Cut.thy:193` (locale: `DBLog_Run_Substrate_Layer2.thy:1780`) | A wellformed run's clean prefix replays to the source state at the run's frontier, restricted to the run's scope. |
| 2 | `accepted_certificate_implies_wellformed_run` | `Virtual_Cut.thy:595` (locale: `DBLog_Cert_Substrate.thy:230`) | An accepted certificate + faithful source observation is witnessed by a wellformed run coherent with the certificate. |
| 3 | `accepted_virtual_cut_sound` | `Virtual_Cut.thy:619` (locale: `DBLog_Cert_Substrate.thy:251`) | Hence an accepted certificate under faithful observation is a virtual cut on its certified scope and frontier. |
| 4 | `accepted_whole_table_anchor_domain_specialization` | `Layer4_Whole_Table.thy:91` (locale: `DBLog_Cert_Substrate.thy:274`) | With a whole-table claim scope, applying the clean prefix reproduces the entire source state at the frontier. |

Results 2–4 are proved inside the `layer3_checker_substrate` locale, which
abstracts the verifier as soundness obligations S1–S4. Those obligations are
discharged for the concrete verifier in `Virtual_Cut.thy`. When you quote
results 2–4, state that they carry the checker-substrate obligations. Otherwise
some of their assumptions are omitted.

### Source-side continuation and restriction

| # | Theorem | Location | Says |
|---|---|---|---|
| 5 | `virtual_cut_state_continuation` | `Continuation.thy:440` | On a wellformed source history, a cut at `f` on `K` extends to any later `f'` by appending exactly the faithful CDC segment for `(f, f']` on `K`. |
| 6 | `virtual_cut_state_restrict_scope` | `Continuation.thy:564` | A cut on `K` restricts to any `K' ⊆ K`. Widening is not stated and would assert agreement on keys never certified. |
| 7 | `whole_table_state_continuation` | `Continuation.thy:604` | The whole-table instance of continuation. It assumes the unrestricted equality at `f`, so it does not turn a scoped cut into a whole-table cut. |
| 8 | `virtual_cut_restrict_to_subscope` | `Continuation.thy:640` (also `DBLog_Cert_Substrate_Inst.thy:61`) | An accepted certificate's cut restricts to a sub-scope **as a source-side state equality**. It does not say that the verifier accepts a restricted certificate. |
| 9 | `accepted_certificate_continuation_sound` | `Continuation.thy:685` (also `DBLog_Cert_Substrate_Inst.thy:83`) | Accessor-level continuation for an accepted certificate, inside the checker-substrate locale, with the assumption that the continuation segment is faithful. |

**Non-vacuity.** `Public_Checker_Witness.thy` constructs a concrete accepted
certificate/evidence pair and a deployment environment in which faithful
observation holds. It applies all nine theorems to those witnesses. Two further
examples show that the two main assumptions are independent: an accepted pair
whose observation is unfaithful, and faithful evidence that the verifier
rejects.

**Modelling constraint.** The run- and certificate-layer results carry a
`linorder` assumption on the key type `'k`, from the deterministic enumeration
in the canonical clean-prefix construction. The state-level forms of theorems
5–8 do not need it.

## 7. Claims this work does not make

Correct these if you see them, including in your own drafts.

| Misreading | Correction |
|---|---|
| “Proves DBLog is correct.” | Proves properties of a *model* of the mechanism under stated assumptions. |
| “Proves exactly-once delivery.” | Delivery is out of scope. No theorem is a delivery claim. |
| “Guarantees the sink converges.” | Destination-side convergence needs a separate sink model. The paper marks it as future work and as a non-claim. |
| “Verifies Debezium / Flink CDC / Netflix's implementation.” | No implementation is verified. Those systems implement the same mechanism. |
| “The checker accepting means the data is right.” | `Accept` yields a virtual cut **only together with** faithful source observation and the checker-substrate obligations. |
| “The proofs discharge the operational assumptions.” | They are assumptions. The kernel cannot check the deployment obligations or the external observation assumption. |
| “A cut on a table's keys gives whole-table correctness.” | Only if the claim scope *is* the whole table (theorem 4). Scope widening is never claimed. |
| “It takes a consistent snapshot at a point in time.” | A virtual cut asserts an equality of outcomes at a frontier. No single source timestamp is claimed across chunk rows. |
| “38 theorems.” | There are 38 *theory files* and nine main theorems. Twenty-four theory files are witnesses and fixtures. |

## 8. Verifying things yourself

```bash
# Find a theorem statement (frozen tree, stable line numbers)
grep -n "theorem accepted_virtual_cut_sound" formal/*.thy

# List every theorem in the development
grep -rn "^theorem " formal/*.thy

# Confirm nothing is admitted. Empty output means clean. (The word
# "axiomatization" does appear in two documentation passages of
# Source_History.thy, which is why this grep anchors to command position.)
grep -rEn "^\s*(sorry|oops|axiomatization|typedecl|consts)\b" formal/*.thy

# Confirm this tree matches the archived artifact byte for byte
curl -sL https://zenodo.org/records/21732790/files/DBLog_Virtual_Cuts-2.1.tar.gz | tar xz
diff -r DBLog_Virtual_Cuts-2.1 formal        # no output = identical

# Full machine check (Isabelle2025-2, several minutes, LaTeX required)
isabelle build -d formal DBLog_Virtual_Cuts
```

The only `typedef` is the source coordinate type in `Source_History.thy`, a
conservative extension over a provably non-empty set with `linorder` and
`order_bot` instances proved rather than assumed.

## 9. Identifiers, versions, citation

| Object | Identifier |
|---|---|
| Paper | arXiv:2605.31475. v1 29 May, v2 12 Jun, v3 8 Aug, v4 13 Aug, **v5 9 Sep 2026** (current). 28 pages, cs.DB + cs.LO, CC BY 4.0. |
| Artifact, latest | Zenodo `10.5281/zenodo.21732790` (version 2.1, 1 Aug 2026), repo tag `v2.1` |
| Artifact, concept DOI | `10.5281/zenodo.20389696`, always resolves to the newest version |
| Artifact, earlier | `10.5281/zenodo.20652511` (2.0) · `10.5281/zenodo.20389697` (1.0) |
| Prior work | 2020 DBLog paper arXiv:2010.12597 and Netflix Tech Blog, Dec 2019 |

ArXiv v2 cites artifact 2.0 (`10.5281/zenodo.20652511`). ArXiv v3, v4 and v5
cite artifact 2.1 (`10.5281/zenodo.21732790`) and carry the same 2.1 corpus as
an ancillary directory. The repository's `formal/` tree is byte-identical to
that v5 ancillary and the Zenodo deposit. Version 2.1 repairs a Layer 3 negative
control that failed to materialize its run (a vacuous control found by external
review). It also adds a regression theory which proves that the repaired
control reaches its comparison. **The nine main theorem statements are
unchanged.** The repaired control's own statement is strictly stronger. Do not
describe v2 as citing 2.1, or v3/v4/v5 as citing 2.0.

BibTeX entries for both objects are in [`README.md`](README.md#citing) and
machine-readable metadata is in [`CITATION.cff`](CITATION.cff). Prefer those
over composing your own.

## 10. Answering common questions

| Question | Read |
|---|---|
| What is a virtual cut? | §5 above and paper §“Virtual Cuts” |
| What exactly is proved? | `docs/THEOREMS.md`: verbatim statements, assumptions, non-claims |
| Does this prove my pipeline is correct? | No. See §7 above. |
| How does DBLog work? | Paper §“The DBLog Mechanism” and the 2020 paper (arXiv:2010.12597) |
| What are the assumptions? | Paper's *Deployment obligations* and *External observation assumption*, and `formal/README.md` → *Main results* |
| Why 38 theories for 9 theorems? | Fourteen carry definitions and results. Twenty-four are constructed witnesses and fixtures which show that the theorems are not vacuous. `formal/README.md` → *Contents* |
| What changed between artifact versions? | §9 above and `docs/PROVENANCE.md` |
