# Intervals

**Usage:** Overlapping events, time ranges.
**Key Trick:** **Always sort by start time** (usually).
**Complexity:** O(N log N) due to sorting.

## 1. Merge Intervals

_Combine overlapping intervals._

```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = []

    for interval in intervals:
        # If list is empty or no overlap
        if not merged or merged[-1][1] < interval[0]:
            merged.append(interval)
        else:
            # Overlap: Extend end time
            merged[-1][1] = max(merged[-1][1], interval[1])

    return merged
```

## 2. Insert Interval

_Insert new interval into sorted list and merge if necessary._

```python
def insert(intervals, newInterval):
    res = []
    i = 0
    n = len(intervals)

    # 1. Add all before (no overlap)
    while i < n and intervals[i][1] < newInterval[0]:
        res.append(intervals[i])
        i += 1

    # 2. Merge overlapping
    while i < n and intervals[i][0] <= newInterval[1]:
        newInterval[0] = min(newInterval[0], intervals[i][0])
        newInterval[1] = max(newInterval[1], intervals[i][1])
        i += 1
    res.append(newInterval)

    # 3. Add all after
    while i < n:
        res.append(intervals[i])
        i += 1

    return res
```

## 3. Non-overlapping Intervals (Greedy)

_Max number of intervals you can keep? OR Min to remove?_
_Trick: Sort by **END time** to leave room for others._

```python
def eraseOverlapIntervals(intervals):
    intervals.sort(key=lambda x: x[1])
    end = float('-inf')
    count = 0  # Count non-overlapping (kept)

    for i in intervals:
        if i[0] >= end:
            end = i[1]
            count += 1

    return len(intervals) - count # To get removed count
```
