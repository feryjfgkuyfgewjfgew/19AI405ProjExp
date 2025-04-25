# Implement a Sudoku Solver From Scratch

### Name: NARESH.R
### Reg.no: 212223240104

## Aim:
To implement a Sudoku puzzle solver in Python using constraint propagation and recursive backtracking techniques to fill a 9x9 grid such that each row, column, and 3x3 subgrid contains all digits from 1 to 9 without repetition.

## Steps to solve the Sudoku Puzzle in Python:
<ol>
  <li>In this method for solving the sudoku puzzle, first, we assign the size of the 2D matrix to a variable M (M*M).</li>
 <li>Then we assign the utility function (puzzle) to print the grid.</li>
<li>Later it will assign num to the row and col.</li>
<li>If we find the same num in the same row or same column or in the specific 3*3 matrix, ‘false’ will be returned.</li>
<li>Then we will check if we have reached the 8th row and 9th column and return true for stopping further backtracking.</li>
<li>Next, we will check if the column value becomes 9 then we move to the next row and column.</li>
<li>Further now we see if the current position of the grid has a value greater than 0, then we iterate for the next column.</li>
<li>After checking if it is a safe place, we move to the next column and then assign the num in the current (row, col) position of the grid. Later we check for the next possibility with the next column.</li>
<li>As our assumption was wrong, we discard the assigned num and then we go for the next assumption with a different num value</li>
</ol>

## Program:
```
# Helper function to cross two strings (e.g., rows and columns)
def cross(a, b):
    return [s + t for s in a for t in b]

# Defining rows, columns, boxes, units, and peers
rows = 'ABCDEFGHI'
cols = '123456789'
boxes = cross(rows, cols)

# Define unit list (all rows, columns and 3x3 squares)
row_units = [cross(r, cols) for r in rows]
column_units = [cross(rows, c) for c in cols]
square_units = [cross(rs, cs) for rs in ('ABC','DEF','GHI') for cs in ('123','456','789')]

unitlist = row_units + column_units + square_units
units = {box: [u for u in unitlist if box in u] for box in boxes}
peers = {box: set(sum(units[box], [])) - {box} for box in boxes}

# Converts grid string to dictionary of {box: value}
def grid_values(grid):
    assert len(grid) == 81
    all_digits = '123456789'
    values = [c if c in all_digits else all_digits for c in grid]
    return dict(zip(boxes, values))

# Display the Sudoku board in a readable format
def display(values):
    width = 1 + max(len(values[s]) for s in boxes)
    line = '+'.join(['-'*(width*3)]*3)
    for r in rows:
        print(''.join(values[r+c].center(width)+('|' if c in '36' else '') for c in cols))
        if r in 'CF': print(line)

# Eliminate values from peers of boxes with a single value
def eliminate(values):
    for box in boxes:
        if len(values[box]) == 1:
            digit = values[box]
            for peer in peers[box]:
                values[peer] = values[peer].replace(digit, '')
    return values

# If only one box in a unit allows a digit, assign it there
def only_choice(values):
    for unit in unitlist:
        for digit in '123456789':
            dplaces = [box for box in unit if digit in values[box]]
            if len(dplaces) == 1:
                values[dplaces[0]] = digit
    return values

# Apply constraint propagation
def reduce_puzzle(values):
    stalled = False
    while not stalled:
        solved_values_before = len([box for box in boxes if len(values[box]) == 1])
        values = eliminate(values)
        values = only_choice(values)
        solved_values_after = len([box for box in boxes if len(values[box]) == 1])
        stalled = solved_values_before == solved_values_after
        if any(len(values[box]) == 0 for box in boxes):
            return False
    return values

# Recursive backtracking search to solve the puzzle
def search(values):
    values = reduce_puzzle(values)
    if values is False:
        return False
    if all(len(values[s]) == 1 for s in boxes):
        return values

    # Choose one box with the fewest possibilities
    n, s = min((len(values[s]), s) for s in boxes if len(values[s]) > 1)

    for digit in values[s]:
        new_values = values.copy()
        new_values[s] = digit
        attempt = search(new_values)
        if attempt:
            return attempt

# Entry point to solve a Sudoku puzzle string
def solve(grid):
    return search(grid_values(grid))

# Example usage
example_grid = '..3.2.6..9..3.5..1..18.64....81.29..7.......8..67.82....26.95..8..2.3..9..5.1.3..'
solution = solve(example_grid)
display(solution)
```

## Output:
![image](https://github.com/user-attachments/assets/5739cd7c-c874-4df6-9fcf-939091a933d1)

## Result:
The Sudoku puzzle was successfully solved using a Python program that applies constraint propagation strategies (elimination and only choice) followed by backtracking search. The solution produced a valid, completed 9x9 Sudoku grid.
