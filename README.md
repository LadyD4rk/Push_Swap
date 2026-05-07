*This project has been created as part of the 42 curriculum by jobraga-.*

# push_swap

## Description

push_swap is a sorting program written in C, developed as part of the 42 school curriculum. Given a list of integers as arguments, it outputs the shortest possible sequence of operations to sort them in ascending order using only two stacks — `a` and `b` — and a restricted set of moves. The challenge is not just to sort, but to do so in as few operations as possible, pushing understanding of algorithmic complexity and optimization.

## Instructions

### Requirements

- A C compiler (`cc`) with the flags `-Wall -Werror -Wextra`
- `make` utility

### Compilation

```bash
git clone https://github.com/jobraga/push_swap.git
cd push_swap
make
```

### Usage

```bash
./push_swap 4 67 3 87 23
```

The program prints the sequence of moves to stdout, one per line. If the input is already sorted, nothing is printed. To check the number of moves generated:

```bash
./push_swap 4 67 3 87 23 | wc -l
```

To verify correctness with a checker:

```bash
ARG="4 67 3 87 23"; ./push_swap $ARG | ./checker $ARG
```

### Available moves

| Move | Description |
|---|---|
| `sa` / `sb` | Swap the top two elements of stack a / b |
| `pa` / `pb` | Push the top of stack b to a / a to b |
| `ra` / `rb` | Rotate stack a / b upward (top goes to bottom) |
| `rr` | Rotate both stacks simultaneously |
| `rra` / `rrb` | Reverse rotate stack a / b (bottom goes to top) |
| `rrr` | Reverse rotate both stacks simultaneously |

### Cleanup

```bash
make clean    # removes object files
make fclean   # removes object files and the executable
make re       # full recompile
```

## Algorithm

push_swap uses the **Turkish algorithm** (also known as the Turk algorithm), a greedy insertion-based approach that is highly efficient in practice for larger inputs.

### Coordinate system

Each node in the list stores two values: `local_a` (its position in stack `a` relative to the top, where negative values mean it's cheaper to reverse-rotate) and `local_b` (the optimal position in stack `b` to insert it). The total cost of moving a node is `abs(local_a) + abs(local_b)`, stored in `total_cust`.

### Normalization

Before sorting, the input values are replaced with their **rank** (1 to N) using a bubble sort on a copy of the input array. This means the algorithm only needs to know relative order, not actual values — simplifying insertion target calculations regardless of the range of numbers.

### The algorithm

**Small cases** are handled separately for maximum efficiency:
- **2 elements**: at most one `sa`
- **3 elements**: hardcoded decision tree, at most 2 moves
- **4–5 elements**: the smallest element(s) are pushed to `b`, the remaining 3 are sorted with the 3-element handler, then pushed back

**Large cases** (6+ elements) use the Turk algorithm:

1. Push the first two elements to stack `b` — these seed the sorted structure in `b`.
2. For each remaining element in `a`, calculate its `total_cust` to reach its correct insertion position in `b`.
3. Select the node with the **lowest total cost** (`calculate_cust`).
4. Move both stacks simultaneously when both require rotation in the same direction (`rr` / `rrr`), then individually for the remainder — this is the key optimization that reduces operation count.
5. Push the selected node to `b`.
6. Repeat until `a` is empty.
7. Rotate `b` so its maximum element is on top, then push everything back to `a` with `pa` — stack `b` was kept in descending order throughout, so this produces a correctly sorted `a`.

**Finding the insertion target** (`best_local_list`): for a given value from `a`, the correct position in `b` is the largest number in `b` that is still smaller than the value being inserted. If no such number exists (the value is smaller than everything in `b`), it is inserted after the current maximum — maintaining `b`'s descending order at all times.

**Why this algorithm?** The Turk algorithm's strength is in the simultaneous rotation step: when a node needs `ra` twice and `rb` twice, `rr` is used twice instead — halving the operation count for that insertion. This makes it consistently efficient without requiring complex look-ahead or backtracking.

## Resources

- [The Turkish Algorithm explained — medium.com](https://medium.com/@ayogun/push-swap-c1f5d2d41e97)
- [push_swap visualizer](https://github.com/o-reo/push_swap_visualizer)
- [42 Docs — push_swap subject](https://harm-smits.github.io/42docs/projects/push_swap)
- [Sorting algorithm complexity — Big-O Cheat Sheet](https://www.bigocheatsheet.com/)

**AI usage:** Claude (claude.ai) was used to help write and structure this README based on the project's source files and the 42 subject requirements. No code was generated or modified with AI assistance.