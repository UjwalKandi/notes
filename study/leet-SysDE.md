Let me write and verify every solution rather than hand you untested code.

Tier 1 verified. Now Tiers 2–4.

Now Tiers 5–6.

**All 27 solutions executed and verified against LeetCode examples plus empty/single/edge inputs.** Every snippet below passed.

---

## 🔴 Tier 1 — Hashmap & Frequency Counting
*Start here. Same mental motion as your log-aggregation scripts.*

```python
from collections import defaultdict, Counter

# 1. Two Sum — O(n) / O(n)
def two_sum(nums, target):
    seen = {}
    for i, n in enumerate(nums):
        if target - n in seen:
            return [seen[target - n], i]
        seen[n] = i
    return []

# 2. Contains Duplicate — O(n) / O(n)
def contains_duplicate(nums):
    seen = set()
    for n in nums:
        if n in seen:
            return True
        seen.add(n)
    return False

# 3. Valid Anagram — O(n) / O(1)  [charset bounded at 26]
def is_anagram(s, t):
    if len(s) != len(t):
        return False
    c = Counter(s)
    for ch in t:
        if c[ch] == 0:
            return False
        c[ch] -= 1
    return True

# 4. Group Anagrams — O(nk) / O(nk)
def group_anagrams(strs):
    g = defaultdict(list)
    for s in strs:
        k = [0] * 26
        for ch in s:
            k[ord(ch) - ord('a')] += 1
        g[tuple(k)].append(s)          # tuple = hashable key
    return list(g.values())

# 5. Top K Frequent — O(n) / O(n)  [bucket sort]
def top_k_frequent(nums, k):
    freq = Counter(nums)
    buckets = [[] for _ in range(len(nums) + 1)]
    for n, c in freq.items():
        buckets[c].append(n)
    out = []
    for c in range(len(buckets) - 1, 0, -1):
        for n in buckets[c]:
            out.append(n)
            if len(out) == k:
                return out
    return out

# 6. First Unique Character — O(n) / O(1)
def first_uniq_char(s):
    c = Counter(s)
    for i, ch in enumerate(s):
        if c[ch] == 1:
            return i
    return -1

# 7. Majority Element — O(n) / O(1)  [Boyer-Moore voting]
def majority_element(nums):
    count, cand = 0, None
    for n in nums:
        if count == 0:
            cand = n
        count += 1 if n == cand else -1
    return cand

# 8. Intersection of Two Arrays II — O(n+m) / O(min(n,m))
def intersect(nums1, nums2):
    if len(nums1) > len(nums2):
        nums1, nums2 = nums2, nums1    # count the smaller one
    c = Counter(nums1)
    out = []
    for n in nums2:
        if c[n] > 0:
            out.append(n)
            c[n] -= 1
    return out
```

> **Pattern:** build dict → count → look up. **Edge cases to voice:** empty input, single element, all duplicates.

---

## 🔴 Tier 2 — Sliding Window
*One template. Learn it once, five problems collapse.*

```python
# 9. Best Time to Buy and Sell Stock — O(n) / O(1)
def max_profit(prices):
    best, lo = 0, float('inf')
    for p in prices:
        lo = min(lo, p)
        best = max(best, p - lo)
    return best

# 10. Maximum Subarray — O(n) / O(1)  [Kadane's]
def max_subarray(nums):
    if not nums:
        return 0
    cur = best = nums[0]
    for n in nums[1:]:
        cur = max(n, cur + n)          # extend or restart
        best = max(best, cur)
    return best

# 11. Longest Substring Without Repeating — O(n) / O(min(n,m))
def length_of_longest_substring(s):
    last, left, best = {}, 0, 0
    for r, ch in enumerate(s):
        if ch in last and last[ch] >= left:
            left = last[ch] + 1        # jump, never move backward
        last[ch] = r
        best = max(best, r - left + 1)
    return best

# 12. Minimum Size Subarray Sum — O(n) / O(1)  [variable window]
def min_subarray_len(target, nums):
    left = total = 0
    best = float('inf')
    for r, n in enumerate(nums):
        total += n
        while total >= target:         # shrink while valid
            best = min(best, r - left + 1)
            total -= nums[left]
            left += 1
    return 0 if best == float('inf') else best

# 13. Maximum Average Subarray I — O(n) / O(1)  [fixed window]
def find_max_average(nums, k):
    w = sum(nums[:k])
    best = w
    for i in range(k, len(nums)):
        w += nums[i] - nums[i - k]     # add right, drop left
        best = max(best, w)
    return best / k
```

> ⚠️ **Say this unprompted on #12:** *"The nested while loop looks quadratic, but `left` only ever moves forward — at most n times total across the whole run. So it's amortized O(n), not O(n²)."* Interviewers probe this specifically.

---

## 🟠 Tier 3 — Two Pointers & Strings
*This tier proves you can avoid extra allocation.*

```python
# 14. Valid Palindrome — O(n) / O(1)
def is_palindrome(s):
    l, r = 0, len(s) - 1
    while l < r:
        while l < r and not s[l].isalnum(): l += 1
        while l < r and not s[r].isalnum(): r -= 1
        if s[l].lower() != s[r].lower():
            return False
        l += 1; r -= 1
    return True

# 15. Two Sum II (sorted input) — O(n) / O(1)
def two_sum_sorted(numbers, target):
    l, r = 0, len(numbers) - 1
    while l < r:
        t = numbers[l] + numbers[r]
        if t == target:
            return [l + 1, r + 1]      # 1-indexed
        if t < target: l += 1
        else: r -= 1
    return []

# 16. Reverse String — O(n) / O(1) in-place
def reverse_string(s):                 # s is List[str]
    l, r = 0, len(s) - 1
    while l < r:
        s[l], s[r] = s[r], s[l]
        l += 1; r -= 1
    return s

# 16b. Reverse Words in a String — O(n) / O(n)
def reverse_words(s):
    return " ".join(reversed(s.split()))   # split() handles extra spaces

# 17. Move Zeroes — O(n) / O(1)
def move_zeroes(nums):
    w = 0                              # slow writer pointer
    for r in range(len(nums)):
        if nums[r] != 0:
            nums[w], nums[r] = nums[r], nums[w]
            w += 1
    return nums

# 18. Merge Sorted Array — O(n+m) / O(1)
def merge_sorted(nums1, m, nums2, n):
    i, j, k = m - 1, n - 1, m + n - 1  # fill BACKWARD
    while j >= 0:
        if i >= 0 and nums1[i] > nums2[j]:
            nums1[k] = nums1[i]; i -= 1
        else:
            nums1[k] = nums2[j]; j -= 1
        k -= 1
    return nums1
```

> **On #18 say:** *"Filling backward from the end avoids overwriting elements in `nums1` I haven't read yet — that's what buys O(1) space."*
> **On #16b:** *"O(n) space because Python strings are immutable. O(1) is only possible with a mutable char array."*

---

## 🟠 Tier 4 — Stack

```python
# 19. Valid Parentheses — O(n) / O(n)
def valid_parentheses(s):
    pairs = {')': '(', ']': '[', '}': '{'}
    st = []
    for ch in s:
        if ch in pairs:
            if not st or st.pop() != pairs[ch]:
                return False
        else:
            st.append(ch)
    return not st                      # leftover openers = invalid

# 20. Min Stack — O(1) ALL operations / O(n)
class MinStack:
    def __init__(self):
        self.st = []                   # store (value, min_so_far)

    def push(self, val):
        m = val if not self.st else min(val, self.st[-1][1])
        self.st.append((val, m))

    def pop(self):
        if not self.st:
            raise IndexError("pop from empty stack")
        return self.st.pop()[0]

    def top(self):
        if not self.st:
            raise IndexError("top from empty stack")
        return self.st[-1][0]

    def getMin(self):
        if not self.st:
            raise IndexError("getMin from empty stack")
        return self.st[-1][1]
```

> ⚠️ **Min Stack was reported with an explicit exception-handling requirement** on `pop()`/`getMin()` against an empty stack. Those guard clauses are half the grade — do not omit them. This is where your ops instinct is an asset: *"In production, malformed state needs to fail loudly, not silently."*

---

## 🟡 Tier 5 — Intervals & Sorting

```python
import heapq

# 21. Merge Intervals — O(n log n) / O(n)
def merge_intervals(intervals):
    if not intervals:
        return []
    intervals.sort(key=lambda x: x[0])
    out = [intervals[0][:]]            # copy, don't mutate input
    for s, e in intervals[1:]:
        if s <= out[-1][1]:            # overlap
            out[-1][1] = max(out[-1][1], e)
        else:
            out.append([s, e])
    return out

# 22. Meeting Rooms — O(n log n) / O(1)
def can_attend_meetings(intervals):
    intervals.sort(key=lambda x: x[0])
    for i in range(1, len(intervals)):
        if intervals[i][0] < intervals[i-1][1]:
            return False
    return True

# 22b. Meeting Rooms II — O(n log n) / O(n)
def min_meeting_rooms(intervals):
    if not intervals:
        return 0
    intervals.sort(key=lambda x: x[0])
    heap = []                          # min-heap of end times
    for s, e in intervals:
        if heap and heap[0] <= s:      # a room freed up
            heapq.heappop(heap)
        heapq.heappush(heap, e)
    return len(heap)                   # peak concurrency
```

> **Say:** *"The sort is the floor here — O(n log n) is unavoidable unless the input arrives presorted."*

---

## 🟡 Tier 6 — Traversal
*Only if time remains. Space is the differentiator, not time.*

```python
from collections import deque

# 23. Number of Islands — O(m·n) / O(m·n)   [BFS avoids recursion limits]
def num_islands(grid):
    if not grid or not grid[0]:
        return 0
    rows, cols = len(grid), len(grid[0])
    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                q = deque([(r, c)])
                grid[r][c] = '0'       # mark visited immediately
                while q:
                    i, j = q.popleft()
                    for di, dj in ((1,0), (-1,0), (0,1), (0,-1)):
                        ni, nj = i + di, j + dj
                        if 0 <= ni < rows and 0 <= nj < cols and grid[ni][nj] == '1':
                            grid[ni][nj] = '0'
                            q.append((ni, nj))
    return count

class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val, self.left, self.right = val, left, right

# 24. Binary Tree Level Order Traversal — O(n) / O(n)
def level_order(root):
    if not root:
        return []
    out, q = [], deque([root])
    while q:
        level = []
        for _ in range(len(q)):        # snapshot size = one level
            node = q.popleft()
            level.append(node.val)
            if node.left:  q.append(node.left)
            if node.right: q.append(node.right)
        out.append(level)
    return out

# 25. Path Sum — O(n) / O(h)
def has_path_sum(root, target):
    if not root:
        return False
    if not root.left and not root.right:   # leaf check is essential
        return root.val == target
    rem = target - root.val
    return has_path_sum(root.left, rem) or has_path_sum(root.right, rem)
```

> **On #25 say:** *"Space is O(h) — recursion depth, not node count. O(log n) if balanced, O(n) if the tree is degenerate."* That distinction separates you from candidates who memorized.
> **On #23:** *"I chose BFS over DFS to avoid blowing the recursion stack on a large all-land grid."*

---

## Study Order & Time Budget

- [ ] **Sun night, 2h** — Tier 1, all 8. Type each from scratch. Do not read the solution first.
- [ ] **Mon AM, 3h** — Tier 2 template until automatic, then Tier 3.
- [ ] **Mon PM, 2h** — Tier 4 + Tier 5, then 2 timed 25-min cold attempts.
- [ ] **Mon PM, 1.5h** — 4 STAR stories out loud (Dover, Epsilon ×2, Sports Excitement).
- [ ] **Mon night, 1h** — Linux triage + `curl` end-to-end, spoken.
- [ ] **Tue AM, 30m** — Re-solve #1, #9, #19 cold. Nothing new.

**The habit that matters more than any single solution:** after writing code, immediately state complexity *and what the space is holding* — "the dict holds at most n keys," "the stack holds unmatched openers." Then dry-run the given example aloud. Multiple Glassdoor reports confirm SysDE rounds ask you to dry-run rather than execute.

## Complexity Cost — All Tiers

The number Amazon wants isn't just the optimal — it's the **naive → optimal** delta and *why*. Both columns below, plus the sentence to say out loud.

---

### 🔴 Tier 1 — Hashmap & Frequency Counting

| # | Problem | Naive | Optimal Time | Space | Say this |
| :-- | :--- | :--- | :--- | :--- | :--- |
| 1 | Two Sum | $O(n^2)$ | $O(n)$ | $O(n)$ | "One pass, store complement in a dict — trade space for time." |
| 2 | Contains Duplicate | $O(n^2)$ | $O(n)$ | $O(n)$ | "Set membership. Or $O(n\log n)$ / $O(1)$ if I sort instead — space-constrained alternative." |
| 3 | Valid Anagram | $O(n\log n)$ | $O(n)$ | $O(1)$ | "$O(1)$ space because the charset is bounded at 26." |
| 4 | Group Anagrams | $O(n^2k)$ | $O(nk)$ | $O(nk)$ | "$k$ = string length. Char-count tuple key beats sorting each string, which is $O(nk\log k)$." |
| 5 | Top K Frequent | $O(n\log n)$ | $O(n\log k)$ heap · $O(n)$ bucket | $O(n)$ | "Heap gives $O(n\log k)$; bucket sort gets true $O(n)$ since frequency is bounded by $n$." |
| 6 | First Unique Char | $O(n^2)$ | $O(n)$ | $O(1)$ | "Two passes, both linear. Fixed 26-slot count array." |
| 7 | Majority Element | $O(n\log n)$ | $O(n)$ | $O(1)$ | "Boyer-Moore voting — $O(1)$ space. Hashmap works but costs $O(n)$." |
| 8 | Intersection of Arrays II | $O(nm)$ | $O(n+m)$ | $O(\min(n,m))$ | "Count the smaller array to minimize space." |

> **Tier signature:** $O(n)$ time, $O(n)$ space. You are always buying time with a dict.

---

### 🔴 Tier 2 — Sliding Window

| # | Problem | Naive | Optimal Time | Space | Say this |
| :-- | :--- | :--- | :--- | :--- | :--- |
| 9 | Best Time to Buy/Sell | $O(n^2)$ | $O(n)$ | $O(1)$ | "Track running min price and best profit. Single pass, constant space." |
| 10 | Maximum Subarray | $O(n^2)$ | $O(n)$ | $O(1)$ | "Kadane's — at each index, extend or restart." |
| 11 | Longest Substring No Repeat | $O(n^3)$ | $O(n)$ | $O(\min(n,m))$ | "$m$ = charset size, so space is effectively $O(1)$ for ASCII." |
| 12 | Min Size Subarray Sum | $O(n^2)$ | $O(n)$ | $O(1)$ | "Each pointer moves at most $n$ times total — amortized linear, not quadratic." |
| 13 | Max Average Subarray I | $O(nk)$ | $O(n)$ | $O(1)$ | "Fixed window: add right, subtract left. No re-summing." |

> **Tier signature:** $O(n)$ time, $O(1)$ space. **The critical talking point:** the nested `while` loop *looks* $O(n^2)$ but each pointer only advances forward, so it's amortized $O(n)$. Interviewers probe this specifically.

---

### 🟠 Tier 3 — Two Pointers & Strings

| # | Problem | Naive | Optimal Time | Space | Say this |
| :-- | :--- | :--- | :--- | :--- | :--- |
| 14 | Valid Palindrome | $O(n)$ + $O(n)$ space | $O(n)$ | $O(1)$ | "Converge from both ends — no reversed copy needed." |
| 15 | Two Sum II (sorted) | $O(n^2)$ | $O(n)$ | $O(1)$ | "Sorted input lets me drop the hashmap entirely — $O(1)$ space." |
| 16 | Reverse String | — | $O(n)$ | $O(1)$ | "In-place swap." |
| 16b | Reverse Words in a String | — | $O(n)$ | $O(n)$ | "$O(n)$ space in Python — strings are immutable. $O(1)$ only with a mutable char array." |
| 17 | Move Zeroes | $O(n)$ + $O(n)$ space | $O(n)$ | $O(1)$ | "Slow/fast writer pointer, in-place." |
| 18 | Merge Sorted Array | $O((n+m)\log(n+m))$ | $O(n+m)$ | $O(1)$ | "Fill backward from the end to avoid overwriting unread elements." |

> **Tier signature:** $O(n)$ time, $O(1)$ space. This tier exists to prove you can avoid extra allocation.

---

### 🟠 Tier 4 — Stack

| # | Problem | Naive | Optimal Time | Space | Say this |
| :-- | :--- | :--- | :--- | :--- | :--- |
| 19 | Valid Parentheses | $O(n^2)$ | $O(n)$ | $O(n)$ | "Space is $O(n)$ worst case — all opening brackets." |
| 20 | Min Stack | $O(n)$ per `getMin` | $O(1)$ **all ops** | $O(n)$ | "Every operation is $O(1)$ by storing min alongside each entry. That's the reported constraint." |

> **Min Stack is the outlier** — cost is quoted **per operation**, not per input. Reported with an explicit $O(n)$ space allowance *and* a requirement to raise on `pop()`/`getMin()` against an empty stack. Handle that exception path; it's half the grade.

---

### 🟡 Tier 5 — Intervals & Sorting

| # | Problem | Naive | Optimal Time | Space | Say this |
| :-- | :--- | :--- | :--- | :--- | :--- |
| 21 | Merge Intervals | $O(n^2)$ | $O(n\log n)$ | $O(n)$ output · $O(\log n)$ aux | "Sort dominates — can't beat $O(n\log n)$ without presorted input." |
| 22 | Meeting Rooms | $O(n^2)$ | $O(n\log n)$ | $O(1)$ | "Sort by start, check adjacent overlap only." |
| 22b | Meeting Rooms II | $O(n^2)$ | $O(n\log n)$ | $O(n)$ | "Min-heap of end times, or a sorted start/end sweep line." |

> **Tier signature:** $O(n\log n)$ — **sort-bound**. Say *"the sort is the floor here"* so they know you identified the bottleneck rather than guessing.

---

### 🟡 Tier 6 — Traversal

| # | Problem | Optimal Time | Space | Say this |
| :-- | :--- | :--- | :--- | :--- |
| 23 | Number of Islands | $O(m \cdot n)$ | $O(m \cdot n)$ | "Every cell visited once. Space is worst-case recursion depth if the whole grid is land." |
| 24 | Level Order Traversal | $O(n)$ | $O(n)$ | "Queue holds the widest level — up to $n/2$ leaves, so $O(n)$." |
| 25 | Path Sum | $O(n)$ | $O(h)$ | "$O(h)$ where $h$ = height: $O(\log n)$ balanced, $O(n)$ degenerate/skewed." |

> **Tier signature:** time is $O(\text{nodes})$ or $O(\text{cells})$. **Space is the differentiator** — it's recursion depth, not node count. Getting $O(h)$ vs $O(n)$ right here separates you from candidates who memorized.

---

## The Four Cost Facts That Do the Most Work

If you retain nothing else from this table:

1. **Hashmap = buy $O(n)$ time with $O(n)$ space.** Tier 1 in one sentence.
2. **Two pointers / sliding window = $O(n)$ time at $O(1)$ space** — and the nested `while` is amortized linear because pointers never move backward.
3. **Anything requiring a sort has an $O(n\log n)$ floor.** Name the sort as the bottleneck.
4. **Recursive space = tree height $O(h)$, not node count.** State the balanced vs skewed cases.

**Delivery rule:** state cost *unprompted* the moment your solution is written, and always name what the space is *holding* ("the dict holds at most $n$ keys", "the stack holds unmatched brackets"). Quoting a bare $O(n)$ reads as memorized. Explaining what occupies the memory reads as understood — and that's the actual difference between a hire and no-hire on a Medium you solved correctly.
