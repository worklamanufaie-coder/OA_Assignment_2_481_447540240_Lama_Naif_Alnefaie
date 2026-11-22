# My Answers - Assignment 2 (CFLP: Tabu Search vs Genetic Algorithm vs Gurobi)

**Student Information:**
- **Student ID:** 447540240
- **Student Name:** Lama Naif Alnefaie
- **Gurobi Version Used:** [x] Academic [ ] Free
- **Date Completed:** 22 November 2026

> Use specific numbers from **YOUR** results (`results_student_<ID>.csv/.xlsx`) and reference the plots by name (e.g., "As shown in `gap_by_size.png`..."). Explain **why**, not only **what**.

---

## Section A: Solution Quality Analysis (4 marks)

### A1 (1 mark) - Overall Mean Optimality Gap

| | Tabu Search | Genetic Algorithm |
|---|---|---|
| Number of instances used (Gurobi optimal) | 144 / 144 | 144 / 144 |
| Mean gap (%) | 10.56 | 11.51 |

**Method with the lower overall gap:** [x] TS [ ] GA

**Comment:**

Gurobi proved optimality on all 144 instances, so every instance is usable for the gap. Tabu Search has the lower mean gap (10.56% against 11.51%, a difference of about 0.95 percentage points). The medians tell the same story (9.46% for TS and 10.21% for GA). Both methods are far from optimal on average: the worst instances reach 26.66% for TS and 30.35% for GA, and 8 TS and 13 GA instances have a gap above 20%. So the metaheuristics with the default parameters give reasonable but not tight solutions, and TS is a little better and a little less spread out (standard deviation 4.98 against 5.61).

---

### A2 (1 mark) - Gap by Instance Size

| | Small | Medium | Large |
|---|---|---|---|
| TS mean gap (%) | 9.49 | 11.28 | 10.90 |
| GA mean gap (%) | 10.44 | 11.99 | 12.09 |

**Does the gap increase with size?** TS: [ ] Yes [x] No  GA: [x] Yes [ ] No

**Comparison of the trends (TS vs GA):**

For GA the gap rises with size (10.44% to 11.99% to 12.09%), but the rise is mostly between small and medium. For TS the gap goes up from small to medium (9.49% to 11.28%) and then comes back down for large (10.90%), so it is not monotonic. TS is below GA at every size, and the distance between them is 0.95 points for small, 0.71 for medium and 1.19 for large. The size effect is modest in this benchmark because the size classes overlap a lot (15-20, 18-24 and 20-28 facilities, with 50-80, 60-75 and 65-70 clients), so a "large" instance is not much bigger than a "medium" one. The visible pattern in `gap_by_size.png` is therefore a small drift upwards, not a strong trend.

---

### A3 (1 mark) - Gap by Difficulty Level

| | Easy | Moderate | Expensive |
|---|---|---|---|
| TS mean gap (%) | 8.52 | 13.78 | 9.37 |
| GA mean gap (%) | 8.69 | 15.69 | 10.14 |

- Hardest difficulty for TS: Moderate (13.78%)
- Hardest difficulty for GA: Moderate (15.69%)

**Explanation:**

Moderate is clearly the hardest level for both methods, and the other two levels are close to each other (easy 8.5-8.7%, expensive 9.4-10.1%). According to the generator, moderate instances combine a fixed-to-service cost ratio around 1 (alpha between 0.8 and 1.5) with tight capacities (capacity/demand between 1.1 and 1.3). With fixed cost and service cost of the same order, opening or closing one facility changes the total cost only slightly in either direction, so there are many near-equal configurations. Tight capacity makes it worse, because the greedy "nearest open facility" assignment used by both heuristics often has to push clients to far facilities, and a single wrong open/closed decision forces expensive re-assignments. Easy instances have cheap fixed costs and loose capacity, so almost any set of open facilities works. Expensive instances need only a few facilities, so the search space that really matters is small. Note that the picture is different for Gurobi: its time is highest on expensive instances (mean 14.49 s) and lowest on easy ones (0.48 s), while the metaheuristics find moderate instances the hardest in terms of quality.

---

### A4 (1 mark) - TS vs GA Head-to-Head (`ts_vs_ga_scatter.png`)

- Fraction of instances where TS has a lower gap than GA: 78 / 144 ( 54.2% )
- Fraction of instances where GA has a lower gap than TS: 2 / 144 ( 1.4% )
- Remaining instances: 64 / 144 ( 44.4% ) end with exactly the same cost for both methods.

**Instance types where one method consistently dominates:**

TS dominates; GA wins on only 2 instances (one medium/expensive and one large/expensive). The advantage of TS is concentrated in moderate and expensive instances: for example TS is better on 13 of 16 medium/moderate, 12 of 16 medium/expensive, 12 of 16 small/moderate and 11 of 16 large/expensive instances. On easy instances the two methods mostly tie (15 of 16 medium/easy and 11 of 16 for small/easy and large/easy). In `ts_vs_ga_scatter.png` the points sit on the diagonal for the ties and below it where TS is better. A likely reason for the many ties is that both algorithms start from, or are seeded with, the same greedy solution and neither improves on it on those instances: TS found its best solution within the first 3 iterations in 30 instances, and GA within the first 3 generations in 53 instances.

---

## Section B: Computational Efficiency Analysis (4 marks)

### B1 (1 mark) - Computation Time and TS Speedup

| Mean time (s) | Small | Medium | Large |
|---|---|---|---|
| Gurobi | 4.50 | 8.34 | 5.91 |
| Tabu Search | 2.46 | 2.55 | 3.03 |
| Genetic Algorithm | 1.99 | 2.35 | 2.67 |

**TS speedup over Gurobi on large instances:** 4.0x (mean of the per-instance speedup factors; the ratio of the mean times is only 1.95x)

**Comment:**

Both metaheuristics take about 2-3 seconds whatever the size, because they run a limited number of iterations/generations and stop early. Gurobi is faster on average than they are only on some instances: its time depends mainly on difficulty, not size (easy about 0.5 s, moderate about 3.8 s, expensive about 14.5 s, with a maximum of 99 s). On easy instances Gurobi (0.4-0.6 s) is actually faster than TS (1.5-2.0 s), and TS is faster than Gurobi on only 36.8% of all instances overall (GA on 39.6%). The 4.0x speedup on large instances is therefore driven by the expensive ones, where Gurobi needs 13.5 s against 3.9 s for TS. The mean of the speedup factors (4.0x) is larger than the ratio of mean times (1.95x) because a few instances where TS stops almost immediately give very large factors; the median speedup of TS over all instances is 0.68x, i.e. below 1.

---

### B2 (1 mark) - GA Speedup vs TS Speedup

- Mean GA speedup over Gurobi (all sizes): 2.41x
- Mean TS speedup over Gurobi (all sizes): 3.28x
- Faster metaheuristic and by how much: it depends on the measure. By mean running time GA is faster (2.34 s against 2.68 s, about 15% less, 0.34 s per instance). By mean speedup factor TS is higher (3.28x against 2.41x).

**Comment:**

The two measures disagree because the speedup factor is an average of ratios (Gurobi time divided by metaheuristic time), so it is pulled up by instances where TS stops after a few iterations and finishes in a fraction of a second (the fastest TS run takes 0.06 s, the fastest GA run 0.99 s). GA has a much more regular running time (standard deviation 0.78 s against 1.65 s for TS) because its generations always cost about the same, while TS ranges from 0.06 s to 9.65 s depending on when it stops. GA is faster than TS on 47.9% of instances. Per size, the mean speedups are 1.77x / 4.06x / 4.01x for TS and 2.11x / 3.07x / 2.04x for GA (small / medium / large). In total, the difference in time between the two metaheuristics is small compared with their difference in quality.

---

### B3 (1 mark) - Growth of Gurobi Time vs Metaheuristic Time

| | Small -> Medium (x) | Medium -> Large (x) |
|---|---|---|
| Gurobi time growth | 1.85x | 0.71x |
| TS time growth | 1.04x | 1.19x |
| GA time growth | 1.18x | 1.14x |

**Comparison:**

Gurobi's mean time goes up by 1.85x from small to medium and then down by 0.71x for large, while TS and GA grow slowly and steadily (TS 1.04x then 1.19x, GA 1.18x then 1.14x). Over the whole range (small to large) the growth is 1.31x for Gurobi, 1.23x for TS and 1.35x for GA, so there is no clear separation. This should not be read as a scaling law: the size classes overlap in this benchmark, and Gurobi's time is dominated by the difficulty level (about 10-20 s on expensive instances against under 1 s on easy ones) and by which instances happen to be in each class. The metaheuristics are almost flat because they have fixed iteration and generation caps and stop early; their cost per iteration grows with the instance size, but the early stopping hides it at these sizes. The real difference between exact and heuristic is the worst case: the slowest Gurobi run takes 99 s while no metaheuristic run takes more than 9.7 s.

---

### B4 (1 mark) - Quality-Speed Trade-off

**Method with the best balance of low gap and low time:** [ ] TS [ ] GA [x] Gurobi

**Justification (with numbers):**

There is a trade-off between quality and speed only for the two metaheuristics: GA is faster by 0.34 s on average (2.34 s against 2.68 s) but TS has a lower gap (10.56% against 11.51%), and since the time difference is small TS is the better of the two. However, at the instance sizes of this assignment (15-28 facilities, 50-80 clients) Gurobi solves every instance to optimality (gap 0%) with a mean of 6.25 s and a median of 1.74 s, which is lower than the medians of TS (2.16 s) and GA (2.13 s). TS and GA save about 3.6 s and 3.9 s on average (6.25 s against 2.68 s and 2.34 s) and pay for it with a gap of 10-12%, and they are even slower than Gurobi on easy instances. So on this benchmark Gurobi has the best balance. The metaheuristics become interesting only where Gurobi is slow (expensive instances, 14.5 s on average and up to 99 s) or for instances much bigger than these.

---

## Section C: Scalability Analysis (3 marks)

### C1 (1 mark) - Gap Heatmap (`gap_heatmap.png`)

- Highest TS gap: (size, difficulty) = (medium, moderate) with 15.08%
- Highest GA gap: (size, difficulty) = (large, moderate) with 16.04%

**Interpretation:**

For both methods the three worst cells of `gap_heatmap.png` are the three moderate cells (TS 12.73% / 15.08% / 13.53% and GA 14.62% / 16.42% / 16.04% for small / medium / large). The difficulty level matters much more than the size: the spread between difficulty levels inside one size (for example 7.9% to 12.7% for TS on small) is larger than the spread between sizes inside one difficulty level. GA is worse than TS in every cell, and its gap grows the most on large/moderate (16.04% against 13.53% for TS). So moderate instances, with balanced costs and tight capacities, are the real challenge for both metaheuristics.

---

### C2 (1 mark) - Expected Scalability to Very Large Instances

**Method expected to scale better:** [x] TS [ ] GA

**Justification (algorithmic complexity and your observed results):**

I expect TS to scale better, with the caveat that both are only moderately affected at the sizes I observed. One TS iteration generates at most about 80 candidate moves (20 per move type), and each candidate needs one re-assignment of the clients and one cost evaluation, so the cost per iteration grows with the number of clients times the number of facilities, but the number of candidates stays bounded by `max_candidates`. A GA generation evaluates about 28 offspring, each one with repair and a greedy assignment, and the chromosome length grows with the number of facilities, so the space (2^m) that a fixed population of 30 has to cover grows much faster than the population. In my results TS also degrades less with size: its gap goes from 9.49% to 10.90% (small to large) against 10.44% to 12.09% for GA, and its running time grows 1.23x against 1.35x for GA. Gurobi, in contrast, has exponential worst-case behaviour (the expensive instances already need up to 99 s), so for instances with 200 facilities and 500 clients I would expect it to become impractical while both metaheuristics would still run in polynomial time per iteration.

---

### C3 (1 mark) - Validity of Gap Measurements When Gurobi Hits the Time Limit

- Instances where Gurobi did not prove optimality (from `gurobi_status`): 0 out of 144

**Effect on the validity of the gaps:**

In my run this problem did not occur: all 144 instances have `gurobi_status = Optimal`, and the slowest instance needed 99.35 s, well below the 300 s time limit, so every gap is measured against a proven optimum and is valid. If Gurobi had stopped at the time limit, its returned solution would only be an upper bound (the best solution found so far), and a gap computed against it would underestimate the true gap of TS and GA, because the true optimum could be lower than the incumbent. It could even become negative if a metaheuristic found a better solution than the incumbent.

**Better benchmark:**

In that case I would measure the gap against the best known lower bound reported by Gurobi (its best bound, which is a valid bound on the optimum), or report Gurobi's own MIP gap together with the metaheuristic gap, or use the best solution found by any method over long runs with several seeds as the reference. It would also help to give Gurobi a longer time limit on those few instances.

---

## Section D: Quality-Time Tradeoff Analysis (3 marks)

### D1 (1 mark) - 10-Second Time Budget (Medium Instances)

**Recommended method:** [x] TS [ ] GA

- Mean TS time / gap on medium instances: 2.55 s / 11.28%
- Mean GA time / gap on medium instances: 2.35 s / 11.99%

**Justification:**

Both methods fit comfortably in 10 s on medium instances: the slowest TS run takes 7.60 s and the slowest GA run 4.72 s. Within the budget I recommend TS because it gives the lower gap (11.28% against 11.99%) for only 0.2 s more on average, and it is better than GA on 26 of the 48 medium instances (13 of 16 moderate, 12 of 16 expensive and 1 of 16 easy; the rest are ties or one GA win). GA is the safer choice only if a hard guarantee is needed, since its worst case is 4.72 s. Gurobi should not be used under this budget: its mean on medium is 8.34 s, but its maximum is 88.43 s, so the budget would be violated on some instances.

---

### D2 (1 mark) - Quality Is the Top Priority

| Size | Recommended approach (TS / GA / Gurobi) | Justification (numbers) |
|---|---|---|
| Small | Gurobi | Gap 0% (proven optimal on all 48 small instances) in 4.50 s on average, against TS 9.49% / 2.46 s and GA 10.44% / 1.99 s. |
| Medium | Gurobi | Gap 0% in 8.34 s on average (maximum 88.43 s) against TS 11.28% and GA 11.99%; the time is acceptable if quality is the only priority. |
| Large | Gurobi | Gap 0% in 5.91 s on average; the slowest large instance still needs under 100 s, well below the 300 s limit, whereas TS has 10.90% and GA 12.09%. |

---

### D3 (1 mark) - Convergence Timing (3-5 convergence plots, pushed in `sample_convergence_plots/`)

**Selected plots:** 099_cflp_large_easy_fac23_cli66, 113_cflp_large_moderate_fac22_cli65, 118_cflp_large_moderate_fac21_cli67, 130_cflp_large_expensive_fac21_cli65, 143_cflp_large_expensive_fac25_cli65 (all in `sample_convergence_plots/`)

| Instance | TS: iteration of best solution / total iterations | GA: generation of best solution / total generations |
|---|---|---|
| 099 (large, easy) | 43 / 83 (52%) | 3 / 33 (9%) |
| 113 (large, moderate) | 156 / 196 (80%) | 1 / 31 (3%) |
| 118 (large, moderate) | 58 / 98 (59%) | 0 / 30 (0%) |
| 130 (large, expensive) | 85 / 125 (68%) | 15 / 45 (33%) |
| 143 (large, expensive) | 38 / 78 (49%) | 2 / 32 (6%) |

(The results file stores the iteration/generation of the best solution. I obtained the totals from the stopping rule: the run stops 40 iterations (TS) or 30 generations (GA) after the last improvement, or at the maximum of 200 / 100. These totals are therefore derived, not logged.)

**What this implies for early stopping:**

Over all 48 large instances the best TS solution appears on average at 29% of the total iterations and the best GA solution at 26% of the total generations. In the selected plots TS finds its best between 49% and 80% of the way, which means that TS is still improving late on some hard instances (113 improves until iteration 156), so cutting TS too early (for example at 20 iterations) would lose quality on those, while the patience of 40 iterations is reasonable. GA, on the other hand, finds its best solution almost at the start (generation 0 to 3 in four of the five plots), and the remaining 30 generations of patience are spent without any improvement: this is a sign of premature convergence of the population, and shows that GA could stop much earlier (or should be given more diversity) without losing anything. Early stopping saves time but, for TS, the stopping threshold should depend on how fast the instance is still improving.

---

## Section E: Algorithm Understanding (3 marks)

### E1 (1 mark) - Tabu Search: Tabu List and Aspiration Criterion

**How the tabu list prevents cycling:**

Tabu Search always moves to the best admissible neighbour, even if it is worse than the current solution. Without a memory it would take a step out of a local optimum and then take the best step back into it, and repeat this forever. The tabu list stores the inverse of each move that was just applied, together with the iteration at which it expires (the tabu tenure, 10 iterations by default). While an inverse move is in the list, it is forbidden, so the search cannot undo its last decisions and is pushed to explore new regions.

**Role of the aspiration criterion:**

A tabu rule can sometimes block a very good move, for instance a move that leads to the best solution ever seen. The aspiration criterion overrides the tabu status when the move gives a total cost lower than the best cost found so far (the global best). This keeps the memory from hiding improvements, while it still blocks moves that only return to old solutions.

**Concrete CFLP example** (a specific tabu move, and when aspiration would override it):

Suppose the search applies the move `('open', 7)`, that is, it opens facility 7. Its inverse `('close', 7)` is added to the tabu list with expiry iteration t + 10, so for the next 10 iterations the search may not close facility 7 again. If at iteration t + 4 closing facility 7 would give a total cost of 31,000 and the best cost found so far is 31,200, the move is tabu, but it satisfies the aspiration criterion because 31,000 < 31,200, so it is accepted. If its cost were 31,500, it would stay forbidden until the tenure expires.

---

### E2 (1 mark) - Genetic Algorithm: Repair Operator and Crossover

**How the repair operator ensures feasibility, and what would happen without it:**

After crossover and mutation a chromosome (the vector of open/closed facilities) can be infeasible, for example when the open facilities do not have enough total capacity for all client demands, or when no facility is open. The repair operator first makes sure that at least one facility is open, then assigns the clients to the nearest open facilities within capacity, and while the solution is still infeasible it opens the closed facility with the largest capacity, until it is feasible or no closed facility is left. Without it, infeasible chromosomes would get an infinite cost in the fitness function, so a large part of the offspring would be wasted, the population would fill with useless individuals, and selection pressure would break down (or the GA would return an infeasible solution).

**How uniform crossover combines two parent CFLP solutions:**

For each facility (gene) a random number decides whether the child takes the value from parent 1 or from parent 2 (probability 0.5), and the second child takes the opposite choice. Example with 6 facilities: parent 1 = 1 0 1 1 0 0 and parent 2 = 0 1 1 0 1 0. If the random mask is (1, 0, 0, 1, 1, 0) (1 means "swap"), child 1 = 0 0 1 0 1 0 and child 2 = 1 1 1 1 0 0. Both children mix the open/closed decisions of the two parents, and they are repaired afterwards if their capacity is not enough.

---

### E3 (1 mark) - Trajectory-Based vs Population-Based Exploration

**Meaning for the exploration of the solution space:**

A trajectory-based method like TS keeps one current solution and moves step by step through neighbouring solutions (open, close, swap or reassign one thing), so it follows a single path and explores one region in depth, using memory to avoid going back. A population-based method like GA keeps many solutions at once and creates new ones by recombining them, so it samples several regions in parallel and can make large jumps in the solution space (crossover changes many facilities at once), but it explores each region less carefully.

| | Advantages for the CFLP | Disadvantages for the CFLP |
|---|---|---|
| Tabu Search (trajectory) | Neighbourhood moves match the structure of the CFLP (open/close/swap/reassign) so it refines a good solution very well; small memory; in my results it has the lower gap (10.56%) | Explores only one path, so the start (greedy solution) matters and it can stay in one region; performance depends on tenure and the candidate list; runtime is irregular (0.06-9.65 s) |
| Genetic Algorithm (population) | Samples many different open/closed patterns at the same time and can jump between regions with crossover; very regular runtime (about 2-3 s) | Weak at fine local refinement; crossover and mutation produce many infeasible chromosomes that need repair; in my results it converged prematurely (best solution in generation 0-3 for 53 instances) and its gap is higher (11.51%) |

---

## Section F: Critical Reflection (3 marks)

### F1 (1 mark) - Most Important TS Hyperparameters

1. `tabu_tenure` (default 10): the number of iterations a reversed move stays forbidden. If it is too short the search cycles, if it is too long it blocks too many moves. With a fixed value 10 TS finds its best solution late on some hard instances (iteration 156 on instance 113) and very early on others, which suggests that a single value is not right for all instances.

2. `max_candidates` (default 20 per move type): the number of neighbours evaluated per move type at each iteration. A larger value gives better moves but makes each iteration slower; since the swap and reassign generators only look at a few options per facility/client, the default evaluates a small part of the neighbourhood, which may explain why the gap stays around 10%.

3. `max_no_improve` (default 40) together with `max_iterations` (200): the stopping rule. With the default, TS stops about 40 iterations after its last improvement; the average iteration of the best solution is only 22-28, so on most instances TS stops after 60-70 iterations, which is fast (about 2.7 s on average) but may stop before a hard instance is exploited.

**What I would change and why:**

I would make the tenure adaptive (longer when the search stagnates, shorter when it improves), increase `max_candidates` for moderate instances, which have the highest gap (13.78%), and add a diversification restart when no improvement is found for some iterations, instead of stopping. These changes target the weakness seen in the results: the gap on moderate instances and the many instances where TS stops very early (30 instances have their best solution in the first 3 iterations).

---

### F2 (1 mark) - Most Important GA Hyperparameters

1. `population_size` (default 30): the number of chromosomes; it controls how much of the space is sampled in each generation and the diversity at the start.

2. `mutation_rate` (default 0.10 per gene): the main source of new open/closed combinations; too small and the population converges, too large and the search becomes random.

3. `max_generations` (100) together with `max_no_improve` (30): the stopping rule. In my results the best solution appears at generation 0-3 in 53 instances and the run then stops after 30 more generations, so on those instances the population stopped improving almost at once.

**Effect of increasing `population_size` / `max_generations` on quality and runtime:**

A larger population samples more patterns and delays premature convergence, so it should lower the gap, but runtime grows roughly linearly with the population size, because every generation evaluates and repairs each offspring. More generations only help if the population still has diversity: in my runs GA often finds its best solution at the beginning, so a higher `max_generations` alone would mostly add runtime without improving quality; it needs to be combined with more diversity (a larger population or a higher or adaptive mutation rate). The runtime grows linearly with both parameters.

---

### F3 (1 mark) - Proposed Algorithmic Improvements

**Improvement for Tabu Search:**

Add a diversification mechanism (restart/perturbation after stagnation) and an adaptive tenure. TS is a trajectory method, so it can be trapped in one region and its result depends on the starting solution; a strong perturbation after stagnation moves it to another region while memory-based diversification stops it from coming back to the same one. Observed: the gap is highest on moderate instances (13.78%), where there are many near-equal local optima, and TS stops early on many instances (the best solution in the first 3 iterations for 30 instances). (This is the direction I test in the bonus part.)

**Improvement for the Genetic Algorithm:**

Make GA memetic, that is, apply a short local search (a few Tabu Search iterations) to the best chromosomes, and add diversity control (adaptive mutation or re-injection of random individuals when the population converges). GA is good at exploring but weak at local refinement, and in my runs it converges prematurely (best solution at generation 0-3 for 53 instances; GA has the higher gap, 11.51%, and is worse than TS on 78 of the 144 instances). A local search would improve the individuals that GA finds, and diversity control would use the generations that are now wasted. (This is also tested in the bonus part.)

---

## BONUS A: Tabu Search Improvement (+5%, optional)

*Leave this section empty if you do not attempt the bonus. See Section 8 of the guide. Your improved notebook (`StudentID_FirstName_LastName_Assignment2_IMPROVED.ipynb`) must be pushed to your repository.*

**(a) Screening results (small/moderate subset):**

| Enhancement tested | Mean gap before (%) | Mean gap after (%) | Improvement |
|---|---|---|---|
| Adaptive tenure | 13.185 | 13.115 | 0.070 |
| Aspiration+ criterion | 13.185 | 13.041 | 0.143 |
| Frequency-based memory | 13.185 | 12.691 | 0.493 |
| Diversification restart | 13.185 | 13.387 | -0.203 (worse) |

**(b) Selected enhancement and justification:**

I chose **frequency-based memory**. It gave the largest gap reduction on the screening subset (0.49 percentage points), at almost the same runtime (2.80 s vs 2.71 s). It penalizes moves that were used many times, so the search is pushed toward parts of the solution space it has not visited, which fits the problem I found in Section F (TS gets trapped in one region on moderate instances). The restart variant was worse than the baseline, and adaptive tenure and aspiration+ helped only a little.

**(c) Full 144-instance results:**

| Metric | Original TS | Improved TS |
|---|---|---|
| Mean gap (%) | 10.558 | 10.344 |
| Mean runtime (s) | 2.68 | 2.62 |

On the full set the gain is smaller than on the screening subset (0.21 points, about 2% relative), because many instances end with the same cost as before.

**Improved results files (names in your repository):** `447540240_Lama_Naif_Alnefaie_Assignment2_IMPROVED.ipynb`, `results_student_447540240_improved.csv/.xlsx`, `visualization_plots_student_447540240_improved/`, `bonus_screening_student_447540240.csv/.png`, `bonus_comparison_student_447540240.csv`, `bonus_comparison_by_size_student_447540240.csv`

---

## BONUS B: Genetic Algorithm Improvement (+5%, optional)

*Leave this section empty if you do not attempt the bonus.*

**(a) Screening results (small/moderate subset):**

| Enhancement tested | Mean gap before (%) | Mean gap after (%) | Improvement |
|---|---|---|---|
| Adaptive mutation | 14.607 | 14.607 | 0.000 |
| Two-point crossover | 14.607 | 14.608 | -0.001 |
| Memetic GA (TS + GA) | 14.607 | 14.040 | 0.567 |
| Diversity preservation | 14.607 | 14.607 | 0.000 |

**(b) Selected enhancement and justification:**

I chose the **memetic GA** (a short 3-iteration Tabu Search applied to the best chromosome in each generation). It was the only variant that changed the gap clearly (0.57 points). Adaptive mutation, two-point crossover and diversity preservation gave the same gap as the baseline, which fits my observation that GA finds its best solution in the first generations and then does not improve, so more diversity alone does not help; local refinement does. The cost is runtime: 6.14 s vs 2.15 s in the screening.

**(c) Full 144-instance results:**

| Metric | Original GA | Improved GA |
|---|---|---|
| Mean gap (%) | 11.506 | 11.070 |
| Mean runtime (s) | 2.34 | 5.79 |

The gap falls by 0.44 points (about 3.8% relative) but the runtime is about 2.5 times higher, so the improvement is a quality-time trade-off.

**Improved results files (names in your repository):** same improved notebook and files as in Bonus A (the improved notebook contains both the TS and GA enhancements).

---

## Additional Comments (Optional)


---

## Declaration

I hereby declare that:
- I have completed this assignment independently
- All numerical results are from MY experiments using MY student ID
- All analysis and interpretations are MY own work
- I have not copied results or answers from other students
- I understand the consequences of academic dishonesty

**Signature:** Lama Naif Alnefaie

**Date:** 22/11/2026

---

**END OF MY ANSWERS**
