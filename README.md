# SMT_project

Counting Game SolverA Z3 SMT Solver-based solution for the Counting game and its attack-resilient variant.Project DescriptionThis project implements two strategies to solve the Counting game:Base Game
The player has 6 different numbers and must combine them using elementary arithmetic operations (+, -, *, /) to get as close as possible to a given goal number.Rules:

Each number can be used only once
Operations are applied sequentially
Division is allowed only if exact (no remainder)
Primary objective: reach the exact goal
Secondary objective: minimize the number of operations


## Resilient Variant
Same as the base game, but with an adversary that can attack the player's last chosen number, replacing it with a number from 0 to 10 to maximize the distance from the goal.
Winning strategy: find the sequence that minimizes the distance in the worst-case attack scenario.


## Technologies Used

Python 3.x
Z3 Theorem Prover - SMT Solver for constraint satisfaction
