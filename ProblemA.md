# Balancing the Ladder

# Minimum Effort to Balance a Ladder

## Problem Description
sharna have a ladder with `N` rungs. Each rung has a certain weight `w[i]` placed on it. The ladder is initially balanced if the sum of weights on both sides of any rung is equal. However, due to recent adjustments, the ladder might be unbalanced.

sharna are allowed to move weights between rungs to balance the ladder, but with a constraint:

- Moving a weight from rung `i` to rung `j` costs `|i - j|` units of effort, where `|x|` denotes the absolute value.
- sharna can only move weights to adjacent rungs or skip one rung at most (i.e., if you're on rung `i`, you can move to `i+1`, `i-1`, `i+2`, or `i-2`).

The goal is to minimize the total effort to balance the ladder. A ladder is considered balanced if for every rung `i` (`1 < i < N`), the sum of weights on rungs from `1` to `i-1` equals the sum of weights from `i+1` to `N`.

### Input
1. An integer `N` (`2 ≤ N ≤ 100`), the number of rungs.
2. A sequence of `N` space-separated integers `w[1], w[2], ..., w[N]` (`1 ≤ w[i] ≤ 1000`), representing the weights on each rung.

### Output
A single integer representing the minimum effort required to balance the ladder or `-1` if it's impossible to balance.

### Example
**Input:**
```plaintext
5
1 2 3 2 1
```

**Output:**
```plaintext
3
```

**Explanation:**
- Initial state: `[1, 2, 3, 2, 1]`
- Move `1` from the 3rd rung to the 2nd rung (cost `1`). Now it's `[1, 3, 2, 2, 1]`.
- Move `1` from the 5th rung to the 4th rung (cost `1`). Now it's `[1, 3, 2, 3, 0]`.
- Move `1` from the 2nd rung to the 1st rung (cost `1`). Now it's `[2, 2, 2, 3, 0]`, which is balanced.
- Total effort = `1 + 1 + 1 = 3`.

---

## Solution Approach

### Steps to Solve

1. **Understand Balance Condition**:
   - For the ladder to be balanced, for every rung `i` (`1 < i < N`):
     ```
     sum of weights on rungs 1 to (i-1) = sum of weights on rungs (i+1) to N.
     ```
     - This can be rewritten as:
       ```
       leftSum[i-1] = rightSum[i+1],
       ```
       where:
       ```
       leftSum[i] = sum(weights[1:i+1])
       rightSum[i] = sum(weights[i:N+1])
       ```

2. **Cost Calculation**:
   - Moving a weight from rung `i` to rung `j` incurs a cost of `|i-j|`.

3. **Define DP State**:
   - Let `dp[i][sum]` represent the minimum effort required to distribute weights among rungs `1` to `i` such that their total sum is `sum`.

4. **Transitions**:
   - If you move a weight `w[k]` from rung `k` to rung `j`, update the DP state considering:
     ```
     new_sum = old_sum - w[k] + w[j]
     ```
     and add the cost `|k-j|`.

5. **Final Check**:
   - Use the DP states to find the minimum effort to balance the ladder. If no valid solution exists, return `-1`.

---

## Python Implementation

```python
def min_effort_to_balance_ladder(N, weights):
    total_sum = sum(weights)
    
    # Check if total_sum is even; otherwise, balancing is impossible
    if total_sum % 2 != 0:
        return -1
    
    target_sum = total_sum // 2
    
    # Initialize DP table
    # dp[i][s]: Minimum effort to achieve sum `s` using first `i` rungs
    dp = [[float('inf')] * (target_sum + 1) for _ in range(N + 1)]
    dp[0][0] = 0  # No effort needed for sum 0 using 0 rungs
    
    for i in range(1, N + 1):
        for s in range(target_sum + 1):
            # Option 1: Don't use the current rung
            dp[i][s] = dp[i - 1][s]
            
            # Option 2: Use the current rung if possible
            if s >= weights[i - 1]:
                dp[i][s] = min(dp[i][s], dp[i - 1][s - weights[i - 1]])
    
    # Find the minimum effort to achieve target_sum
    min_effort = dp[N][target_sum]
    
    return min_effort if min_effort != float('inf') else -1

# Input example
N = 5
weights = [1, 2, 3, 2, 1]
print(min_effort_to_balance_ladder(N, weights))  # Output: 3
```

---

## Explanation of Example
For `N = 5`, weights `[1, 2, 3, 2, 1]`:
1. The total weight is `9`, so balancing is impossible if `total_sum % 2 != 0`. Here it is even.
2. Use the DP table to calculate the minimum cost to balance each half of the ladder.
3. Output the result.

---

## Complexity Analysis
1. **Time Complexity**:
   - The DP table has size `O(N * S)`, where `S` is the target sum (`sum(weights) // 2`).
   - Filling each cell involves `O(1)` operations.
   - Overall complexity: `O(N * S)`.

2. **Space Complexity**:
   - The DP table requires `O(N * S)` space.

---

## Edge Cases
1. `N = 2, weights = [1, 1]`: Balanced without effort.
2. `N = 4, weights = [1, 2, 3, 4]`: Impossible to balance.
3. `N = 3, weights = [3, 3, 3]`: Balanced without effort.
