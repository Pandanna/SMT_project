<h1 align="center">Counting Game Solver</h1>

<p align="center">
  <em>Reaching a target number with six numbers and four operations, solved with an SMT solver, even when an adversary tampers with the last move.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=FFD43B" alt="Python">
  <img src="https://img.shields.io/badge/Z3_SMT_Solver-0078D4?style=for-the-badge" alt="Z3 SMT Solver">
  <img src="https://img.shields.io/badge/Jupyter-F37626?style=for-the-badge&logo=jupyter&logoColor=white" alt="Jupyter">
</p>

---

## Table of Contents

- [About](#about)
- [The Game](#the-game)
- [How It Works](#how-it-works)
- [Example](#example)
- [Usage](#usage)
- [Author](#author)

---

## About

This project solves the **Counting game** by describing it as a set of logical constraints and letting the [Z3](https://github.com/Z3Prover/z3) SMT solver find the answer, instead of searching all the combinations by hand.

It covers two versions of the game:

- the **base game**, where the player looks for the sequence of operations that gets closest to the goal;
- an **attack-resilient variant**, where an adversary can replace the player's last number, and the player must choose the sequence that holds up best in the worst case.

---

## The Game

The player has six numbers and must combine them with the elementary operations (`+`, `-`, `*`, `/`) to get as close as possible to a goal number.

**Rules**

- Each number can be used only once.
- Operations are applied in sequence, each one to the running result.
- Division is allowed only when it is exact (no remainder).
- First objective: reach the goal exactly.
- Second objective: use as few numbers as possible.

**Resilient variant**

Same rules, with one addition: an adversary can replace the last number the player chose with any number from 0 to 10, picking the one that pushes the result furthest from the goal. The winning strategy is the sequence with the smallest distance from the goal in that worst case.

---

## How It Works

For a sequence of `k` numbers the problem is encoded with integer variables:

| Variable | Meaning |
|---|---|
| `idx_i` | Which number is used at step `i` |
| `op_i` | Which operation is applied at step `i` (`+`, `-`, `*`, `/`) |
| `res_i` | The running result after step `i` |
| `distance` | Absolute distance between the final result and the goal |

The constraints state that the chosen numbers are all different, that each result follows from the previous one through the chosen operation, and that a division is valid only when exact.

**Base game.** The solver tries sequences of increasing length, from 2 numbers up to all 6. For each length it repeatedly asks for a solution with a strictly smaller distance until none exists, and it stops as soon as the goal is reached exactly. This gives the closest result with the fewest numbers.

**Resilient variant.** The last step is modelled once for every possible attack value (0 to 10). A `worst_distance` variable is constrained to be at least as large as the distance produced by each attack, and the solver minimises it. The result is the strategy whose worst case is the least bad.

---

## Example

Numbers `[1, 3, 5, 8, 10, 50]`, goal `462`.

**Base game**: the goal is reached exactly with four numbers.

```text
Initial number: 50
Step 1: - 3 → 47
Step 2: * 10 → 470
Step 3: - 8 → 462
Final number: 462
Distance from goal: 0
```

**Resilient variant**: the solver gives up the exact result in exchange for safety. Without an attack the sequence ends 2 away from the goal; under the worst possible attack it ends only 5 away.

```text
Initial number: 50
Step 1: - 5 → 45
Step 2: * 10 → 450
Step 3: - 1 → 449
Step 4: + 8 → 457
Step 5: + 3 → 460
Final number: 460
Distance from goal: 2
Worst attacker: 0
Distance after worst attack: 5
```

The base solution would be a poor choice here: its last step is a subtraction of 8, and an adversary replacing that 8 with 0 would leave the result at 470.

---

## Usage

Install the dependencies:

```bash
pip install z3-solver notebook
```

Open the notebook and run it:

```bash
jupyter notebook project.ipynb
```

The two entry points take the list of numbers and the goal:

```python
CountingStrategy([1, 3, 5, 8, 10, 50], 462)
CountingStrategyResilient([1, 3, 5, 8, 10, 50], 462)
```

---

## Author

**Anna Chiara Mameli** · [github.com/Pandanna](https://github.com/Pandanna)
