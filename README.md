<!-- solve-problem:ddc50e2d-baef-477f-9154-f6839a1bb9f6 -->
# Certified planted-clique computation frontier

A [Tangle Solve problem](https://solve.tangle.tools/problems/ddc50e2d-baef-477f-9154-f6839a1bb9f6).

## What is the problem?

In the planted clique model, we sample a graph from \(G(n, 1/2)\) (Erdős–Rényi, each edge present independently with probability 1/2), then pick a random set \(K\) of \(k\) vertices and add all edges among them. The computational task: given the graph, recover \(K\) (or output any clique of size \(\ge k\)). Information-theoretically, recovery is possible once \(k \gtrsim 2\log_2 n\); the spectral algorithm of Alon, Krivelevich, and Sudakov recovers the clique whenever \(k = \Omega(\sqrt{n})\), and no polynomial-time algorithm is known (or believed to exist) below \(k = o(\sqrt{n})\). This problem is **not** to close that gap theoretically. It is an **experimental measurement** problem: build a rigorous empirical frontier mapping, for actual implementations on real hardware, how recovery probability, wall-clock time, memory, and energy scale with \(n\) and \(k\) across the theoretically conjectured transition region \(k \in [\Theta(\log n), \Theta(\sqrt{n})]\]. The methodology must be preregistered (generators, parameters, and analysis fixed before running), use held-out seeds not used for tuning, and verify recovered cliques with an independent checker.

## Why does it matters?

A hardware-normalized empirical frontier would tell us where asymptotic complexity distinctions (e.g., \(\sqrt{n}\) vs. \(n^{1/3}\) vs. statistical threshold) actually become observable at practical sizes, directly informing cryptographers who use planted-clique-hardness assumptions and statisticians studying computational gaps in sparse PCA / submatrix detection. It also blocks a recurring failure mode in the literature: cherry-picked "we beat the threshold" claims from adversarially chosen instances or seeds. Without it, claims about practical hardness of average-case problems remain anecdotal.

## What's already known?

- Alon, Krivelevich, and Sudakov (1998): \(O(n^2)\)-time (near-linear in edges) spectral and degree-counting algorithms recovering planted cliques for \(k = \Omega(\sqrt{n})\), plus matching analysis showing simple statistics fail below that.
- Barak et al. (2016), arXiv:1604.03084: nearly tight sum-of-squares lower bounds — degree-\(d\) SoS cannot distinguish planted cliques of size \(k \ll n^{1/(d+1)}\) (roughly), giving the best evidence that \(k \approx \sqrt{n}\) is a real computational barrier in restricted models.
- Heuristics (message passing, simulated annealing, deep walks) empirically recover cliques somewhat below \(\sqrt{n}\) for moderate \(n\), but published experiments lack preregistration, seed discipline, energy accounting, and independent verification — exactly what this problem demands. No standardized, reproducible benchmark suite exists.

## What would count as solving it?

A published, reproducible benchmark protocol plus results satisfying:
1. **Preregistration**: generator code, parameter grid \((n, k)\), success criterion, and analysis pipeline frozen (with timestamped commit/registry entry) before any measurement runs.
2. **Held-out evaluation**: seeds split into tuning vs. evaluation; frontier reported only on held-out seeds, with confidence intervals from multiple seeds.
3. **Independent verification**: recovered cliques checked by code written by someone other than (or independent of) the algorithm authors, confirming edge-completeness and size.
4. **Metrics**: recovery probability, wall-clock time, peak memory, and energy (e.g., via RAPL/interface meters) as functions of \(n\) and \(k\), spanning at least \(k \in [2\log_2 n, c\sqrt{n}] \cdot (1\pm\epsilon)\) and \(n\) over ≥2 orders of magnitude.
5. Public artifact (code, data, environment specs) enabling third-party reruns.

## How to start

1. Read Alon–Krivelevich–Sudakov (1998) Sections 2–4 for the spectral/degree algorithms you'll implement as the baseline above-threshold solver; read Barak et al. (2016) Sections 1–2 only to locate the theoretical transition regions to target — no SoS implementation is needed for phase one.
2. Fix the statistical design first: choose the \((n, k)\) grid (log-spaced \(n\); \(k\) sweeping across \(c\sqrt{n}\), \(n^{1/3}\), \(n^{1/4}\), \(2\log_2 n\)), number of seeds for 95% CIs, and a preregistered success definition (exact recovery vs. clique of size ≥ \(k - t\)).
3. Write the independent checker first (trivial: verify all \(\binom{|C|}{2}\) edges in returned set \(C\)), and a seeded generator using a standard PRNG with published seeds.
4. Main obstacle: energy measurement reproducibility across hardware; mitigate by normalizing to a reference machine and reporting hardware specs, or using cloud instances with fixed SKUs. Second obstacle: run time explosion at large \(n\) for below-threshold heuristics — budget time-boxing into the preregistration.

## Key references

- N. Alon, M. Krivelevich, B. Sudakov, "Finding a large hidden clique in a random graph," *Random Structures & Algorithms* 13(3–4):457–466, 1998. Gives near-linear-time spectral and combinatorial algorithms recovering planted cliques for \(k = \Omega(\sqrt{n})\) and analyzes failure of simpler methods. https://arxiv.org/abs/math/9905116 (verify link/citation details)
- B. Barak, S. B. Hopkins, J. Kelner, P. K. Kothari, A. Moitra, A. Potechin, "A nearly tight sum-of-squares lower bound for the planted clique problem," 2016. Proves degree-\(d\) SoS cannot certify/distinguish planted cliques below size \(\approx n^{1/(d+1)}\) (verify exact exponent), delimiting the restricted-model hardness landscape. https://arxiv.org/abs/1604.03084

## Warnings

- Do not conflate "recovered a large clique" with "recovered the planted clique": below \(k \approx 2\log_2 n\) the maximum clique isn't the planted one; success definitions must be preregistered accordingly.
- The problem is explicitly *empirical*; a new theoretical algorithm or lower bound does not solve it.
- Beware seed reuse and adaptive tuning — these are precisely the failure modes the preregistration requirement targets.
- "Energy" is hardware- and configuration-dependent; without normalization the frontier is not comparable across machines.
- The census record cites a "fleet:gpt6pro" 2026 source of uncertain reliability; treat any specific numbers derived from it as unverified.

---
*`ALGO-000242` · flagged for review*

## Acceptance guidance

A published, reproducible benchmark protocol plus results satisfying:
1. **Preregistration**: generator code, parameter grid \((n, k)\), success criterion, and analysis pipeline frozen (with timestamped commit/registry entry) before any measurement runs.
2. **Held-out evaluation**: seeds split into tuning vs. evaluation; frontier reported only on held-out seeds, with confidence intervals from multiple seeds.
3. **Independent verification**: recovered cliques checked by code written by someone other than (or independent of) the algorithm authors, confirming edge-completeness and size.
4. **Metrics**: recovery probability, wall-clock time, peak memory, and energy (e.g., via RAPL/interface meters) as functions of \(n\) and \(k\), spanning at least \(k \in [2\log_2 n, c\sqrt{n}] \cdot (1\pm\epsilon)\) and \(n\) over ≥2 orders of magnitude.
5. Public artifact (code, data, environment specs) enabling third-party reruns.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/ddc50e2d-baef-477f-9154-f6839a1bb9f6?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: ALGO-000242.

- [Public source catalog](https://drewstone.github.io/problems/#/p/ALGO-000242)
