# Sudoku Solving

### Introduction

Sudoku is a common puzzle game in which players place digits on a 9x9 grid. The rules are simple: in each finished puzzle, each digit 1-9 must show at least once in each row, column, and local 3x3 grid.

Sudoku solving programs are relatively easy to write using depth-first search: the program iteratively places moves until it places one that violates the rules; then, it backs up and tries again. In this exercise, you'll be testing a mediocre sudoku solver. There are 4 bugs in the program; unlike in previous assignments, however, they are turned off unless specifically turned on. You'll need to use a combination of property tests, mocks and example-based tests to find all the bugs.

### Program design and invocation

The Sudoku solver is a Python class with the following interface and pseudocode in its implementation:

```python
import time

class SudokuBoard:
    pass

class UnsolvableException(Exception):
    pass

class TookTooLongException(Exception):
    pass

class SudokuSolver:
    def __init__(self, timeout: float = 30.0, bug_number: int = 0) -> None:
        pass

    def solve(self, SudokuBoard) -> tuple[SudokuBoard, int]:
        start = time.time()

        solution = solve_the_sudoku()

        end = time.time()

        return solution, end - start
```

The solver will throw an `UnsolvableException` if it cannot find a solution. If more than `timeout` seconds elapse while computing a solution, it will throw a `TookTooLongException`. Note that in order to implement the timeout, the solver internally uses a separate thread to run the solving process.

The `SudokuBoard` class is an object for easily manipulating sudoku boards. You may find the `from_string` and `__str__` methods particularly useful. The code is reproduced below:

```python
from dataclasses import dataclass

@dataclass
class SudokuBoard:
    """
    Represents a 9x9 Sudoku board.
    Cells are integers 0..9, where 0 means "empty".
    """

    grid: list[list[int]]

    def __post_init__(self) -> None:
        if len(self.grid) != 9 or any(len(row) != 9 for row in self.grid):
            raise ValueError("Grid must be 9x9")

        for r in range(9):
            for c in range(9):
                v = self.grid[r][c]
                if v is None:
                    self.grid[r][c] = 0
                elif not (0 <= int(v) <= 9):
                    raise ValueError("Cell values must be integers in 0..9")
                else:
                    self.grid[r][c] = int(v)

    @classmethod
    def from_string(cls, s: str) -> SudokuBoard:
        """
        Create a board from a string with 81 characters containing digits,
        '.' or '0' for blanks. Whitespace is ignored, as are pipes, dashes and plus signs.
        """
        chars = [ch for ch in s if not ch.isspace() and ch not in "|-+"]

        grid: list[list[int]] = [[0] * 9 for _ in range(9)]
        for idx, ch in enumerate(chars):
            r, c = divmod(idx, 9)
            if ch in (".", "0"):
                grid[r][c] = 0
            elif ch.isdigit():
                grid[r][c] = int(ch)
            else:
                raise ValueError(f"Unexpected character in board: {ch!r}")

        return cls(grid)

    def clone(self) -> SudokuBoard:
        return SudokuBoard([row[:] for row in self.grid])

    def get(self, row: int, col: int) -> int:
        return self.grid[row][col]

    def set(self, row: int, col: int, value: int) -> None:
        if not (0 <= value <= 9):
            raise ValueError("Value must be in 0..9")
        self.grid[row][col] = value

    def __eq__(self, other: object) -> bool:
        if not isinstance(other, SudokuBoard):
            return NotImplemented
        return self.grid == other.grid

    def __str__(self) -> str:
        """
        Pretty-print the board with row/column separators, e.g.:

        +-------+-------+-------+
        | 5 3 . | . 7 . | . . . |
        | 6 . . | 1 9 5 | . . . |
        ...

        The output can be loaded as a SudokuBoard with from_string.
        """
        lines = []
        sep = "+-------+-------+-------+"
        for r in range(9):
            if r % 3 == 0:
                lines.append(sep)
            row_cells = []
            for c in range(9):
                v = self.grid[r][c]
                ch = "." if v == 0 else str(v)
                row_cells.append(ch)
                if c % 3 == 2 and c != 8:
                    row_cells.append("|")
            lines.append("| " + " ".join(row_cells) + " |")
        lines.append(sep)
        return "\n".join(lines)

```

### Deliverable

In the tests you turn in, you **must** invoke the solver with the `solve_sudoku` function:

```python
result, time_elapsed = solve_sudoku(board, bug_number=0, timeout=10.0)
```

The `bug_number` argument controls which bug you are looking for in the program; unlike in previous assignments, not all bugs are turned on at once in the "buggy" version of the program. Pass 1, 2, 3, or 4 to look for a separate bug; passing 0 invokes the program without bugs.

In order to get credit for finding a bug, you should invoke the bug number you found in that test:

```python
def test_found_bug_one(self):
  board = your_code_here
  solution, time_elapsed = solve_sudoku(board, bug_number=1)
  self.assertEqual(soluton, some_other_thing)
```

You only need to turn in these example-based tests to get credit, but if you use property tests to find the bugs, turn them in commented out as well.

Make sure not to turn in tests that run excessively long. The autograder has a 1-minute timeout. None of the bugs require you to run the solver for a long time in order to detect them.

### Advice

My advice is to use property tests to find the bugs and then finalize them with example-based tests. You'll need to do some manipulation of time to find everything.

Good luck!# Final_SoftwareTesting
