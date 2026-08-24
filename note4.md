# Map Function

## Matrix Multiplication

Given:

```text
M = [1 2
     3 4]

N = [5 6
     7 8]
```

For matrix multiplication:

```text
P = M × N
```

The formula is:

```text
P[i][k] = Σ M[i][j] × N[j][k]
```

### Map function

The main idea:

* Get all `M` and `N` elements having the same `(i, k)`
* Group them based on `(i, k)`
* For each group, match them using the common `j`
* Multiply:

```text
M[i][j] × N[j][k]
```

* The mapper produces intermediate key-value pairs like:

```text
((i,k), value)
```

### Example

For `P[0][0]`:

```text
M[0][0] × N[0][0] = 1 × 5 = 5
M[0][1] × N[1][0] = 2 × 7 = 14
```

So:

```text
P[0][0] = 5 + 14 = 19
```

For `P[0][1]`:

```text
M[0][0] × N[0][1] = 1 × 6 = 6
M[0][1] × N[1][1] = 2 × 8 = 16
```

So:

```text
P[0][1] = 6 + 16 = 22
```

For `P[1][0]`:

```text
M[1][0] × N[0][0] = 3 × 5 = 15
M[1][1] × N[1][0] = 4 × 7 = 28
```

So:

```text
P[1][0] = 15 + 28 = 43
```

For `P[1][1]`:

```text
M[1][0] × N[0][1] = 3 × 6 = 18
M[1][1] × N[1][1] = 4 × 8 = 32
```

So:

```text
P[1][1] = 18 + 32 = 50
```

Final matrix:

```text
P = [19 22
     43 50]
```

## Map → Shuffle/Sort → Reduce

### Map

Mapper generates intermediate values for every required `(i,k)`.

Example:

```text
(0,0) → 1×5
(0,0) → 2×7

(0,1) → 1×6
(0,1) → 2×8

(1,0) → 3×5
(1,0) → 4×7

(1,1) → 3×6
(1,1) → 4×8
```

### Shuffle and Sort

Same keys are grouped together:

```text
(0,0) → [5, 14]
(0,1) → [6, 16]
(1,0) → [15, 28]
(1,1) → [18, 32]
```

### Reduce

Reducer adds all the values belonging to the same `(i,k)`:

```text
(0,0) → 5 + 14 = 19
(0,1) → 6 + 16 = 22
(1,0) → 15 + 28 = 43
(1,1) → 18 + 32 = 50
```

Therefore:

```text
P = [19 22
     43 50]
```

### Easy way to remember

```text
MAP
 ↓
Find matching (i,k)
 ↓
Multiply M[i][j] × N[j][k]
 ↓
SHUFFLE / GROUP
 ↓
Same (i,k) values together
 ↓
REDUCE
 ↓
Add all products
 ↓
Final P[i][k]
```

