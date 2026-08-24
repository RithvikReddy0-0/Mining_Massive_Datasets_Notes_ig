# Dividing the Streams and Classifying Them into Tuples

## Overview

Stream data needs to be organized and sampled efficiently. This involves dividing streams into manageable units (tuples), classifying them, and applying sampling techniques to maintain representative data without storing everything.

---

## Stream Classes of Tuples

A **stream class** is a collection of tuples that share common characteristics. When processing data streams, we need to:

1. **Identify key components** that define tuple groups
2. **Group related tuples** together
3. **Apply sampling strategies** to representative units

### Key Concepts

- **Primary Key**: A subset of components that uniquely identifies a tuple or group of tuples.
- **Sampling Unit**: Consists of all tuples with the same key value.
- All tuples within a sampling unit are treated as a single entity for sampling decisions.

---

## Example 1: Network Traffic Stream

Imagine a network monitoring system receiving packets:

```
Tuple Format: (Source_IP, Destination_IP, Packet_Size, Timestamp)

Stream Data:
1. (192.168.1.100, 8.8.8.8, 1024, 10:00:01)
2. (192.168.1.101, 8.8.8.8, 512, 10:00:02)
3. (192.168.1.100, 8.8.8.8, 2048, 10:00:03)
4. (192.168.1.102, 1.1.1.1, 1024, 10:00:04)
5. (192.168.1.100, 8.8.8.8, 1536, 10:00:05)
6. (192.168.1.101, 1.1.1.1, 2560, 10:00:06)
```

**Primary Key**: (Source_IP, Destination_IP)

**Sampling Units** (grouped by key):

- **Unit 1**: (192.168.1.100, 8.8.8.8) → Tuples 1, 3, 5
- **Unit 2**: (192.168.1.101, 8.8.8.8) → Tuple 2
- **Unit 3**: (192.168.1.102, 1.1.1.1) → Tuple 4
- **Unit 4**: (192.168.1.101, 1.1.1.1) → Tuple 6

---

## Hash-Based Sampling with Threshold

### The Problem

When a stream grows larger, we need to:
- Reduce sampling memory overhead
- Adaptively adjust sample size based on stream growth
- Maintain representation across all key groups

### The Solution: Hash-Based Threshold Method

**Core Idea:**
- Compute a **hash value** for each tuple's key
- **Include the tuple** if: `hash(key) ≤ threshold`
- **Lower the threshold** as the stream grows to maintain a target sample size

### Algorithm

```
For each incoming tuple T with key K:
    hash_value = hash(K)
    if hash_value ≤ threshold:
        Include tuple T in sample
    else:
        Discard tuple T

If sample size exceeds target:
    Decrease threshold
    Recompute which tuples belong in sample

If stream grows significantly:
    Recompute hash(K) if needed
    Adjust threshold accordingly
```

### Example 2: Temperature Sensor Stream with Adaptive Sampling

**Scenario**: Sensors across a city sending temperature readings.

```
Tuple Format: (Sensor_ID, Location, Temperature, Timestamp)

Initial Threshold: 0.5 (keep 50% of keys)

Stream Data:
1. (S001, Downtown, 25.3, 10:00:00) → hash(S001) = 0.23 ✓ INCLUDE
2. (S002, North, 22.1, 10:00:01) → hash(S002) = 0.67 ✗ EXCLUDE
3. (S001, Downtown, 25.5, 10:00:02) → hash(S001) = 0.23 ✓ INCLUDE
4. (S003, South, 28.4, 10:00:03) → hash(S003) = 0.12 ✓ INCLUDE
5. (S002, North, 21.9, 10:00:04) → hash(S002) = 0.67 ✗ EXCLUDE
6. (S004, East, 23.8, 10:00:05) → hash(S004) = 0.88 ✗ EXCLUDE

Sample After 6 tuples: [Tuple 1, 3, 4]
Sample Size: 3 out of 6 = 50% ✓

Stream Continues Growing...

After 1000 tuples, if sample size needs reduction:
    Current threshold: 0.5
    Reduce to: 0.25 (keep 25% of keys)
    
    Reapply to all data:
    S001: 0.23 ≤ 0.25 ✓ KEEP
    S002: 0.67 > 0.25 ✗ DROP
    S003: 0.12 ≤ 0.25 ✓ KEEP
    S004: 0.88 > 0.25 ✗ DROP
    
    New sample contains only tuples with keys S001 and S003
```

### Benefits of Hash-Based Sampling

| Aspect | Benefit |
|--------|---------|
| **Adaptive Size** | Threshold can be adjusted without reprocessing old data |
| **Deterministic** | Same key always produces same hash (consistent sampling) |
| **Uniform Distribution** | Keys are uniformly distributed across hash values |
| **Memory Efficient** | No need to store which keys to keep; just check hash |

---

## DGMI Algorithm (Data Grouping and Moment Index)

The DGMI algorithm is a technique for efficient stream summarization using binary representations and bucketing.

### Core Concept

Instead of storing individual tuples, we use a compact binary representation to track which data elements have been seen and organize them into time-based buckets.

### Step-by-Step Explanation

#### Step 1: Binary Stream Representation

Each position in the stream is represented as a **bit** in a binary number:
- `1` = tuple with key present in this position
- `0` = tuple with key absent in this position

**Example:**

```
Stream: T1(S001), T2(S002), T3(S001), T4(S003), T5(S001), T6(S002), T7(S003), T8(S001)

Keys present:
Position 1: S001 → 1
Position 2: S002 → 1
Position 3: S001 → 1
Position 4: S003 → 1
Position 5: S001 → 1
Position 6: S002 → 1
Position 7: S003 → 1
Position 8: S001 → 1

Binary representation: 11111111 (all positions have some key)

For key S001 specifically: 10101101
For key S002 specifically: 01000100
For key S003 specifically: 00010010
```

#### Step 2: Window Definition

Define a **window size** (e.g., last N bits of the binary representation).

```
Full binary stream: 011010011 (9 positions)
Window size: 4 bits
Window (last 4 bits): 0011 (positions 6-9)
```

#### Step 3: Creation of Buckets for Each Key

For every key that appears in the stream, create a **bucket** to track its occurrences.

**Example with 3 sensors:**

```
Stream (9 time steps): 011010011
Timestamps: 1, 2, 3, 4, 5, 6, 7, 8, 9

Sensor S001 appears at positions: 2, 3, 5, 9 → Binary: 011010001
Sensor S002 appears at positions: 4, 6 → Binary: 000110100
Sensor S003 appears at positions: 1, 7, 8 → Binary: 100000110

Bucket for S001: [2, 3, 5, 9] or binary 011010001
Bucket for S002: [4, 6] or binary 000110100
Bucket for S003: [1, 7, 8] or binary 100000110
```

#### Step 4: Timestamp Concepts

**Original timestamp**: Actual position in the stream (starts from 1).

```
Position: 1  2  3  4  5  6  7  8  9
Data:     S3 S1 S1 S2 S1 S2 S3 S3 S1
Timestamp:1  2  3  4  5  6  7  8  9
```

**Bucket timestamp**: The relative position within a bucket or window.

```
S001 occurrences: positions 2, 3, 5, 9
Bucket timestamps: 1, 2, 3, 4 (relative to bucket)
```

---

## Complete DGMI Example

### Scenario: Web Server Request Stream

```
Stream of HTTP requests from different IP addresses

Full Stream (15 requests):
Time: 1  2  3  4  5  6  7  8  9  10 11 12 13 14 15
IP:   A  B  A  C  A  B  C  A  B  A  C  B  A  C  A

Where:
A = 192.168.1.100
B = 192.168.1.101
C = 192.168.1.102
```

### Step 1: Binary Representation

```
Full binary: 111111111111111 (all timestamps have requests)

For IP A: 1 0 1 0 1 0 0 1 0 1 0 0 1 0 1 → positions 1,3,5,8,10,13,15
For IP B: 0 1 0 0 0 1 0 0 1 0 0 1 0 0 0 → positions 2,6,9,12
For IP C: 0 0 0 1 0 0 1 0 0 0 1 0 0 1 0 → positions 4,7,11,14
```

### Step 2: Window (Last 6 time steps)

```
Window positions: 10-15
Full data: A B A C A C A B A C B A C
Positions: 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15
                              ↑  ↑  ↑  ↑  ↑  ↑
                            Window (10-15)

Window data: A   C   B   A   C   A
Positions:   10  11  12  13  14  15
Binary:      1   1   1   1   1   1
```

### Step 3: Buckets for Each Key (in Window)

```
Bucket for A (positions 10, 13, 15):
    - Absolute timestamps: [10, 13, 15]
    - Relative to window start (10): [1, 4, 6]
    - Binary: 100011 (within 6-bit window)

Bucket for B (position 12):
    - Absolute timestamp: [12]
    - Relative to window start (10): [3]
    - Binary: 000100

Bucket for C (positions 11, 14):
    - Absolute timestamp: [11, 14]
    - Relative to window start (10): [2, 5]
    - Binary: 010010
```

### Step 4: Summary Statistics

```
Key A: 5 total occurrences, 3 in window
Key B: 4 total occurrences, 1 in window
Key C: 4 total occurrences, 2 in window

Memory usage: 3 buckets × 6 bits = 18 bits (vs. 15 × full tuple storage)
```

---

## Practical Application: Frequency Estimation

### Use Case: Estimating Query Frequency

```
Given: Stream of database queries
Goal: Estimate which queries are most frequent
Method: Use DGMI buckets to track query patterns

Stream (20 queries):
Q1 Q2 Q1 Q3 Q1 Q2 Q1 Q4 Q2 Q1 Q3 Q2 Q1 Q4 Q1 Q3 Q1 Q2 Q4 Q1

Binary representations:
Q1: 1 0 1 0 1 0 1 0 0 1 0 0 1 0 1 0 1 0 0 1 → 9 occurrences
Q2: 0 1 0 0 0 1 0 0 1 0 0 1 0 0 0 1 0 1 0 0 → 5 occurrences
Q3: 0 0 0 1 0 0 0 0 0 0 1 0 0 0 0 0 0 0 0 0 → 2 occurrences
Q4: 0 0 0 0 0 0 0 1 0 0 0 0 0 1 0 0 0 0 1 0 → 3 occurrences

Window (last 8 queries, positions 13-20):
Q1: 1 0 1 0 1 0 0 1 → 4 occurrences in window
Q2: 0 0 0 1 0 1 0 0 → 2 occurrences in window
Q3: 0 0 0 0 0 0 0 0 → 0 occurrences in window
Q4: 0 1 0 0 0 0 1 0 → 2 occurrences in window

Estimated frequencies:
- Q1 is most frequent (45% overall, 50% recent)
- Q2 is moderate (25% overall, 25% recent)
- Q3 and Q4 are less frequent
```

---

## Key Takeaways

| Concept | Purpose |
|---------|---------|
| **Stream Classes** | Organize tuples by shared keys |
| **Sampling Units** | Group all tuples with same key together |
| **Hash Threshold** | Adaptively control sample size as stream grows |
| **DGMI Buckets** | Compress stream data using binary representation |
| **Windows** | Focus on recent, relevant data only |
| **Binary Representation** | Efficient storage of tuple presence/absence |

---

## Summary

Efficient stream processing requires:

1. **Classifying** tuples into groups by key
2. **Sampling** representative tuples using hash-based thresholds
3. **Bucketing** data by key and time using binary representations
4. **Windowing** to focus on recent data
5. **Summarizing** instead of storing raw tuples

This combination enables processing of massive, continuous data streams with minimal memory and computation.