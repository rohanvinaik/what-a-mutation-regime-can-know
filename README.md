# What a Mutation Regime Can Know About a Function

**A provably-sufficient output-mutation regime, its knowability boundary, and the intent residual**

Rohan Vinaik, November 2026.

DOI: [10.5281/zenodo.24042390](https://doi.org/10.5281/zenodo.24042390)

This repository holds the preprint and its artifact: a Lean 4 development that
checks the theorems, a standard-library Python checker that decides the main
theorem's condition and returns a re-checkable counterexample, and the §9.5
experiment with every per-test record its numbers come from.

| Path | Contents |
| --- | --- |
| `paper/` | The preprint (12 pages, IEEE conference format): PDF and LaTeX. |
| `proofs/` | The Lean 4 development, `adequacy_completeness.lean` (38 theorems, no `sorry`), with `Audit.lean` pinning every theorem's axioms. |
| `checker/` | Theorem 3.2 as a decision procedure, with tests that compare it against the definition exhaustively on three-valued return types. |
| `experiment/` | The §9.5 study on four Python libraries, with the pinned library commits, the scripts, and the raw results. |
| `THEOREM_LEDGER.md` | Every numbered claim of the paper and how it is established: mechanized, via lemmas, paper proof, or empirical. |
| `ARTIFACT.md` | How to run each of the three parts. |

## What the paper proves

Mutation testing scores a test suite by the mutants it kills, and a full score
is read as adequacy. The paper asks what a full score against one family of
mutation operators guarantees about the score against another, for every
program and every suite, and answers it for output mutation, where an operator
perturbs the returned value. For adequacy an output operator is exactly its
footprint, the set of values it changes.

- **Theorem 3.2.** A finite regime is sufficient for a target class iff every
  target footprint is the union of the regime's footprints it contains. The
  proof is constructive: when the condition fails it produces a program and a
  suite on which the regime scores one and the target does not. An infinite
  regime obeys a finite-avoidance form of the same law (Theorem 3.2b).
- **Corollary 4.1.** On n output values the minimal sufficient regime is the n
  singleton value guards; Descartes-style constants are sufficient iff n = 2.
- **Theorem 4.5.** A full absolute score certifies exactly output coverage:
  every value the program can return was observed, and nothing more.
- **Theorem 5.2.** Deciding sufficiency is polynomial for value tables,
  decidable for Presburger-definable footprints, and undecidable for computable
  operators.
- **§9.** Under a weak oracle the theory relativizes and characterizes the
  pseudo-tested method.
- **§9.5.** On four Python libraries (131 functions, 985 mutants), 9% of
  functions have a type-preserving constant pseudo-tested under the value
  oracle, and the model's prediction about constant mutants held for every
  function where it was testable, except two whose type-changing constant
  crashes a test before its assertion.

The theory covers output and extreme mutation of pure total functions. It makes
no claim about program-text mutation or about the coupling hypothesis for
program-text faults.

## Checking the Lean development

Lean and Mathlib are pinned to v4.28.0; `lake-manifest.json` records the
Mathlib commit. From `proofs/`:

```sh
lake exe cache get
lake build
```

The default targets are the development and its audit. `Audit.lean` holds, for
every theorem, its exact `#print axioms` output as a `#guard_msgs` expectation,
so the build fails if any theorem acquires `sorryAx` or an axiom beyond
`propext`, `Classical.choice` and `Quot.sound`. A clean build with the Mathlib
cache takes about two minutes on a laptop.

| Record | Value |
| --- | --- |
| Theorems | 38, no `sorry` |
| Axioms used | propext, Classical.choice, Quot.sound |
| Lean toolchain | leanprover/lean4:v4.28.0 |
| Mathlib | v4.28.0, commit 8f9d9cff6bd7 |
| Ledger | 21 claims mechanized, 2 via lemmas, 5 paper proofs, 1 empirical |

## Running the checker and the experiment

```sh
cd checker && python -m unittest test_sufficiency -v
python sufficiency.py examples/example_4_4.json
```

```sh
cd experiment && python run.py && python analyze.py
```

The experiment needs `uv`, `git` and network access; it clones the four
libraries at their pinned commits and takes about ten minutes with eight
workers. `experiment/results/` already holds the records the paper reports.

## Citing

See `CITATION.cff`.

## License

The manuscript (`paper/`) is released under CC BY 4.0; see `LICENSE-PAPER.md`.
The Lean development and the Python code are released under the MIT License;
see `LICENSE`.
