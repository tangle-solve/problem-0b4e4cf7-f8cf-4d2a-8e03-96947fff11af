<!-- solve-problem:0b4e4cf7-f8cf-4d2a-8e03-96947fff11af -->
# Factor-two superstring approximation with reverse complements

A [Tangle Solve problem](https://solve.tangle.tools/problems/0b4e4cf7-f8cf-4d2a-8e03-96947fff11af).

## What is the problem?

The **Shortest Common Superstring with Reverse Complements (SCS-RC)** problem. Input: a set of strings \(S = \{s_1, \dots, s_n\}\) over the DNA alphabet \(\{\text{A},\text{C},\text{G},\text{T}\}\). For each string, \(\overline{s_i}\) denotes its Watson–Crick reverse complement (reverse the string, then map A↔T, C↔G). Output: a shortest string \(T\) and a choice of orientation \(\sigma_i \in \{s_i, \overline{s_i}\}\) for each \(i\), such that every \(\sigma_i\) is a substring of \(T\).

Equivalently, it is SCS over the "orbits" \(\{s_i, \overline{s_i}\}\), where each orbit must be covered at least once (covering both orientations is allowed but you pay for one). This is the natural formalization of double-stranded DNA assembly: a sequencing read could have originated from either strand.

The census asks specifically: **is a factor-2 polynomial-time approximation achievable?** The classical SCS admits a 2-approximation (greedy; or via cycle-cover doubling after the Gallant–Maier–Storer conjecture, proven by Blum et al. via Engelfriet–Hogeworst). For SCS-RC the best known is \(8/3\) (Yamano–Shibuya). Ambiguity note: "factor two" could mean an exact 2-approximation or any constant strictly below \(8/3\); the natural reading is achieving ratio 2, ideally matching SCS. Whether SCS-RC is even in APX (constant-factor approximable) vs. some orientation-specific hardness is settled as constant-factor approximable per the census, with hardness precluding exact optimization.

## Why does it matter?

SCS is the canonical abstraction of genome assembly, and real sequencing data has unknown strand orientation per read — SCS-RC is the honest model. A 2-approximation would match the classical SCS guarantee and likely transfer to assembly pipelines (overlap-layout-consensus scaffolding) that currently use heuristics with no orientation-aware guarantee. Progress here also tests whether the cycle-cover/MGST machinery underlying the SCS 2-approximation survives symmetry under the reverse-complement involution — a question of independent interest in string algorithmics.

## What's already known?

- **Classical SCS:** Greedy gives 2 (Gallant–Maier–Storer conjecture, proven via Blum et al.'s reduction to a special TSP path / Engelfriet–Hogeworst's max-2-SAT-restricted variant). Best known is 2; best lower bound (Max-SNP) shows no PTAS.
- **SCS-RC:** Yamano–Shibuya (WABI 2026) give an \(8/3\)-approximation and prove hardness ruling out exact optimization (and, presumably, some approximability barrier — verify the exact hardness constant from the paper). This is the state of the art per the census.
- **Likely technique gap:** the SCS 2-approximation chains (i) a maximum cycle-cover on the overlap graph, (ii) deletion of the longest overlap per cycle to stitch cycles into a path, (iii) a greedy/assignment argument converting to a superstring. With reverse complements, the overlap graph has signed/orbital structure — overlaps between chosen orientations interact, and the "delete heaviest edge per cycle" step loses a \(4/3\) factor rather than matching — plausibly the source of \(8/3\). (Verify the mechanism in the paper.)

*(Caveat: the 2026 reference is beyond my training data; the above reconstruction of techniques is informed conjecture, not citation.)*

## What would count as solving it?

1. **Primary goal:** a polynomial-time algorithm for SCS-RC with approximation ratio **at most 2** (i.e., \(\le 2\cdot\mathrm{OPT}\) on every instance), with proof.
2. **Partial credit / intermediate results:** any ratio strictly below \(8/3\); or a ratio-2 algorithm for restricted cases (e.g., strings where \(s_i \neq \overline{s_j}\) for \(i \neq j\), all equal-length reads, no string containing another).
3. **Lower-bound variant:** an inapproximability result showing ratio 2 (or any \(c < 8/3\)) is impossible under P≠NP — this would resolve the census question negatively.
4. **Structural result:** prove or refute the reverse-complement analogue of the greedy conjecture (greedy superstring under orbit-choice has ratio 2), which would yield the algorithm.

A referee accepts: a full algorithm + approximation-ratio proof, or a matching hardness reduction.

## How to start

1. Read Yamano–Shibuya (WABI 2026) first — identify exactly where their analysis loses the gap between \(8/3\) and 2; the loss point tells you what to attack.
2. Re-derive the classical 2-approximation from Blum et al. (SODA 1994) / the Engelfriet–Hogeworst special-TSP-path result, then attempt to carry each lemma over to the signed/orbital setting; formalize SCS-RC as TSP on an "orbit graph" where each orbit offers two antipodal vertex choices.
3. Check the greedy conjecture analogue experimentally: implement greedy SCS-RC (at each step merge the orbit-pair with max overlap over all orientation choices) and test against exact optima on small random instances.
4. Main obstacle: orientation choices couple across merges — a merge fixing \(s_i\)'s orientation constrains future overlaps with \(s_j\) and \(\overline{s_j}\) simultaneously; the exchange/locality arguments used in SCS analyses break.

## Key references

- Yamano & Shibuya, *Improved Approximation Algorithms and Hardness Results for Shortest Common Superstring with Reverse Complements*, WABI 2026, DOI: 10.4230/LIPIcs.WABI.2026.2. State of the art: \(8/3\)-approximation plus hardness results. (Note: I cannot verify contents of a 2026 publication from training data; obtain and read the actual paper.)
- Blum, Jiang, Li, Tromp, Yannakakis, *Linear approximation of shortest superstrings*, JACM 41(4), 1994. The classical 2-approximation for SCS via maximum cycle covers and a special TSP path variant — the machinery any improvement must adapt.
- Engelfriet & Hogeworst (?), the "special TSP path" result used to close the greedy conjecture. (Verify exact attribution; it resolves the Gallant–Maier–Storer greedy conjecture.)
- Gallant, Maier, Storer, *On finding minimal length superstrings*, JCSS 20, 1980. Original SCS greedy conjecture and problem framing.
- Middendorf on superstring variants / shortcuts (?): relevant for cycle-cover-to-path conversion; verify. — **omit if unverifiable.**

## Warnings

- I cannot independently verify the Yamano–Shibuya paper's contents or exact constants; everything attributed to it comes from the census record. Fetch and confirm before building on it.
- Beware degenerate cases: strings equal to their own reverse complement (palindromes), \(s_i = \overline{s_j}\) for distinct \(i,j\), and strings containing others — definitions of "shortest" can shift (each orbit counted once vs. each original string).
- The naive reduction "replace each string by its orbit and run SCS" fails: SCS over the doubled set may include both orientations, and SCS-RC optimum can differ from classical SCS optimum on either orientation choice by more than constant care.
- The classical SCS greedy-conjecture proof is notoriously delicate; do not assume lemmas transfer verbatim under the involution — the counterexample space includes orientation-consistency constraints absent classically.
- "Factor two" in the title is the target, not a claim; do not misread the census as asserting existence.

---
*`ALGO-000168` · unverified*

## Acceptance guidance

1. **Primary goal:** a polynomial-time algorithm for SCS-RC with approximation ratio **at most 2** (i.e., \(\le 2\cdot\mathrm{OPT}\) on every instance), with proof.
2. **Partial credit / intermediate results:** any ratio strictly below \(8/3\); or a ratio-2 algorithm for restricted cases (e.g., strings where \(s_i \neq \overline{s_j}\) for \(i \neq j\), all equal-length reads, no string containing another).
3. **Lower-bound variant:** an inapproximability result showing ratio 2 (or any \(c < 8/3\)) is impossible under P≠NP — this would resolve the census question negatively.
4. **Structural result:** prove or refute the reverse-complement analogue of the greedy conjecture (greedy superstring under orbit-choice has ratio 2), which would yield the algorithm.

A referee accepts: a full algorithm + approximation-ratio proof, or a matching hardness reduction.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/0b4e4cf7-f8cf-4d2a-8e03-96947fff11af?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000168.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000168)
