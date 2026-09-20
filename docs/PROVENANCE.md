# Provenance: paper versions and artifact versions

The **paper** on arXiv and the **formal development** on Zenodo are versioned
separately. This file records both histories, including the period when the
paper cited an older artifact version than the newest one.

## Identifiers

| Object | Identifier |
|---|---|
| Paper | [arXiv:2605.31475](https://arxiv.org/abs/2605.31475), cs.DB primary, cs.LO cross-list, CC BY 4.0 |
| Artifact, always-latest | [10.5281/zenodo.20389696](https://doi.org/10.5281/zenodo.20389696) (concept DOI) |
| Artifact, this repository | `formal/`, byte-identical to version 2.1 |
| Prior work | [arXiv:2010.12597](https://arxiv.org/abs/2010.12597) (2020 DBLog paper) |

## Paper versions

| Version | Date | Notes |
|---|---|---|
| v1 | 2026-05-29 | first posting |
| v2 | 2026-06-12 | 31 pages, 5 figures. Cites formal artifact 2.0. |
| v3 | 2026-08-08 | 31 pages, 5 figures. Updates the cited and ancillary formal artifact to 2.1, adopts the `Machine-checked` status label, and adds practitioner clarifications. |
| v4 | 2026-08-13 | 29 pages, 5 figures. Readability revision with rewritten abstract, introduction, headings, and captions. |
| **v5** | **2026-09-09** | **Current version.** 28 pages, 5 figures. Wording revision in every section, with a rewritten abstract and conclusion. Cites and carries formal artifact 2.1, unchanged. The manuscript files in `paper/` are this version. |

## Artifact versions

| Version | DOI | Date | Theories | What changed |
|---|---|---|---|---|
| 1.0 | [10.5281/zenodo.20389697](https://doi.org/10.5281/zenodo.20389697) | 2026-05-26 | 17 | First deposit. Witnesses and counterexample fixtures were introduced by `axiomatization`. |
| 2.0 | [10.5281/zenodo.20652511](https://doi.org/10.5281/zenodo.20652511) | 2026-06-12 | 37 | Foundations rebuilt conservatively and **without axioms**. Every witness and fixture is now a constructed instance. **The nine main theorem statements are unchanged.** |
| **2.1** | [10.5281/zenodo.21732790](https://doi.org/10.5281/zenodo.21732790) | 2026-08-01 | 38 | Fixture repair (below). **The nine main theorem statements are unchanged.** The repaired control's own statement is strictly stronger. |

Each version is also a tag in this repository: [`v1.0`](../../../tree/v1.0),
[`v2.0`](../../../tree/v2.0), [`v2.1`](../../../tree/v2.1). The tags preserve
the layout each version was published with. The corpus moved under `formal/`
only when this repository became the paper's information hub.

## The 2.0 / 2.1 history

ArXiv v2 cites artifact version 2.0 (`10.5281/zenodo.20652511`), which was the
current deposit when v2 was posted on 2026-06-12. Artifact 2.1 was published on
2026-08-01. ArXiv v3, v4 and v5 cite version 2.1, and all three carry
`DBLog_Virtual_Cuts-2.1` as an ancillary directory. The `formal/` tree in this
repository is byte-identical to that v5 ancillary and to the version 2.1 Zenodo
deposit.

An external review of the 2.0 artifact found that one negative control in the
Layer 3 fixtures was **vacuous**: the same-evidence control failed to
materialize its run, so it never reached the accessor-agreement comparison it
advertised. The control passed without making that comparison. No theorem
statement was affected, and no proof depended on it.

Version 2.1 makes two changes to the theories:

1. The control in `Layer3_Fixtures_Inst.thy` now materializes its run, so it
   makes the comparison it advertises.
2. A new kernel-checked theory `Layer3_Defect_Regressions.thy` proves that the
   control materializes its run and reaches the comparison, so the defect
   cannot return unnoticed.

Four other files change with them: `ROOT` registers the new theory, `README.md`
counts and lists it and carries the 2.1 release identification, and
`document/root.tex` and `document/root.bib` name version 2.1. No other file
differs between the 2.0 and 2.1 archives.

The nine main theorem statements are identical across 2.0 and 2.1. Readers
of arXiv v2 should expect the repaired fixture and regression theory when they
compare its cited artifact with this repository. Readers of arXiv v3, v4 or v5
see the same 2.1 corpus in the paper ancillary, Zenodo deposit, and `formal/`
tree.

## Which identifier to cite

- **Concept DOI** `10.5281/zenodo.20389696`: use it for "the formal
  development" in general. It resolves to the newest version.
- **Version DOI**: use it when you need the exact bytes, for example in a
  build log, a review, a reproduction, or a claim about a specific theory file.
- **This repository**: use it for reading and browsing. The Zenodo deposit
  remains the archival identifier. The repository is a mirror with added
  context.

## Checking the mirror

```bash
curl -sL https://zenodo.org/records/21732790/files/DBLog_Virtual_Cuts-2.1.tar.gz | tar xz
diff -r DBLog_Virtual_Cuts-2.1 formal    # no output means identical
```

## Verification environment

Both 2.0 and 2.1 are checked with **Isabelle2025-2**
(`ISABELLE_IDENTIFIER=Isabelle2025-2`), using `HOL-Library` from the
distribution only. No Archive of Formal Proofs entry is required. The session
builds without `sorry` and generates its own entry document, which needs a LaTeX
toolchain.
