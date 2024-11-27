# CI2024_lab3

In this lab I tried to implement a solution for the N^2-1 Puzzle.

## Problem description 

The **N²-1 puzzle problem** is a classic combinatorial optimization challenge involving a sliding puzzle. It consists of:

- A square grid with \( N^2 - 1 \) numbered tiles and 1 empty space, where \( N \) is the grid's dimension.
- Example configurations:
  - \( 3 \times 3 \) grid: 8 tiles (numbered 1 through 8) and 1 blank space.
  - \( 4 \times 4 \) grid: 15 tiles and 1 blank space.

#### Objective
Rearrange the tiles from a given starting configuration to a target configuration (often sequentially ordered, with the blank in the bottom-right corner) by sliding adjacent tiles into the empty space.

#### Key Features
- **Moves**: Only adjacent tiles can be moved into the blank space.
- **Complexity**: The puzzle's difficulty comes from constraints on tile movement and the fact that not all configurations are solvable.

#### Solvability
The solvability of a configuration depends on:
1. The number of **inversions** (pairs of tiles out of order).
2. The grid's dimensions.

The N²-1 puzzle is a benchmark problem for evaluating optimization and search strategies due to its well-defined state space and computational complexity.


## Methodology

I attempted to solve the problem using depth-first with the following code:

```python
def depth_limited_search(initial_state: np.ndarray, final_state: np.ndarray, max_depth: int) -> list[action] or None:
    stack = deque([(initial_state, [], 0)])  # Stack of (current state, path to reach it, current depth)
    visited = set()  # Set of visited states for current path only
    optimum = matrix_score(final_state)

    while stack:
        current_state, path, depth = stack.pop()
        current_score = matrix_score(current_state)

        # Check if we reached the goal
        if current_score == optimum:
            return path
        
        # # Backtrack if depth limit reached
        if depth >= max_depth:
            continue
        
        # Add current state to visited set (track only in current path)
        visited.add(current_score)
        
        # Generate and iterate over all possible moves
        for act in available_actions(current_state):
            next_state = do_action(current_state, act)
            
            # Check if the next state has already been visited in the current path
            if matrix_score(next_state) not in visited:
                # Add the new state and path to stack, increase depth
                stack.append((next_state, path + [act], depth + 1))
        
        # Remove the current state from visited set after backtracking
        #visited.remove(matrix_score(current_state))
    
    return None  # Return None if no solution is found within depth limit

# Iterative Deepening Depth-First Search (IDDFS) wrapper function
def iterative_deepening_dfs(initial_state: np.ndarray, final_state: np.ndarray, max_depth: int = 50) -> list[action] or None:
    for depth in range(1, max_depth + 1):
        result = depth_limited_search(initial_state, final_state, depth)
        if result is not None:
            return result
    return None
```

However, this solution is not able to solve 15 puzzle, or bigger ones.