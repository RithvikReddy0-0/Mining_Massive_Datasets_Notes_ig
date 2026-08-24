# Locality Sensitive Hashing (LSH)

* **Locality Sensitive Hashing (LSH)** is used to find **similar items within the same dataset** efficiently.

## LSH

* LSH focuses on finding **similar pairs** without comparing every item with every other item in the entire dataset.
* It uses special **hash functions** that try to map **similar items into the same bucket**.
* Comparisons are then performed **only among the items that fall into the same bucket**.
* This reduces the number of comparisons significantly and makes similarity search much faster.

### Why LSH?

Normally, if we have `n` documents, comparing every possible pair requires approximately:

`O(n²)` comparisons.

LSH reduces this by first grouping potentially similar items into **buckets**, and then comparing only the items within those buckets.

---

# Application of Similarity

One important application of LSH is finding **similar documents/items** using similarity measures such as **Jaccard Similarity**.

## 1. Jaccard Similarity

Jaccard Similarity measures how similar two **sets** are.

### Formula

`Sim(S, T) = |S ∩ T| / |S ∪ T|`

Where:

* `S` = first set
* `T` = second set
* `S ∩ T` = elements common to both sets
* `S ∪ T` = all unique elements present in either set

The value of Jaccard Similarity lies between **0 and 1**:

* `0` → No common elements
* `1` → Both sets are exactly the same

### Example

Suppose:

`S = {a, b, c, d}`

`T = {c, d, e, f}`

Intersection:

`S ∩ T = {c, d}`

Union:

`S ∪ T = {a, b, c, d, e, f}`

Therefore:

`Jaccard(S,T) = 2/6 = 0.333`

So, the two sets have a **Jaccard Similarity of 0.333**.

> **Note:** Since Jaccard works with sets, repeated elements are ignored.

Example:

`S = {a, a, b, c}`

becomes:

`S = {a, b, c}`

---

# Applications of Similarity

### 1. Plagiarism Check

* Compare documents based on their common words or `k-shingles`.
* If two documents have a **high Jaccard similarity**, they may contain similar content.

### 2. Mirror Pages

* Detect duplicate or nearly duplicate web pages.
* Web pages with very similar sets of words/shingles are likely to be duplicates or mirror pages.

### 3. Articles from the Same Source

* Articles can be represented as sets of words or shingles.
* Articles with high similarity may have been copied from, or generated from, the same source.

---

# Collaborative Filtering as a Similar Sets Problem

**Collaborative Filtering** can be treated as a **similar sets problem**.

* An item can be recommended to a user based on the **purchase/rating/like history** of other users.
* If two users have purchased or liked many of the **same items**, they can be considered similar.
* Jaccard Similarity can be used to measure the similarity between their item sets.

### Example

`User A = {Phone, Laptop, Headphones, Mouse}`

`User B = {Laptop, Headphones, Keyboard, Monitor}`

Common items:

`A ∩ B = {Laptop, Headphones}`

All unique items:

`A ∪ B = {Phone, Laptop, Headphones, Mouse, Keyboard, Monitor}`

Therefore:

`Jaccard(A,B) = 2/6 = 0.333`

So, User A and User B have a similarity of **0.333**.

### Recommendation

If **User A** and **User B** are highly similar, and:

`User B` purchased an item that `User A` has **not** purchased,

then that item can potentially be **recommended to User A**.

### In simple words:

`Similar Users → Find Items They Like → Recommend Unseen Items`

> **Key idea:** Two customers are considered similar when their sets of purchased/liked items have a **high Jaccard Similarity**.

---

# Questions

## Q1. Compute Jaccard Similarity of each pair

Given:

`A = {1,2,3,4}`

`B = {2,3,5,7}`

`C = {2,4,6}`

### A and B

Intersection:

`A ∩ B = {2,3}`

Union:

`A ∪ B = {1,2,3,4,5,7}`

Therefore:

`J(A,B) = 2/6 = 0.333`

### A and C

Intersection:

`A ∩ C = {2,4}`

Union:

`A ∪ C = {1,2,3,4,6}`

Therefore:

`J(A,C) = 2/5 = 0.4`

### B and C

Intersection:

`B ∩ C = {2}`

Union:

`B ∪ C = {2,3,4,5,6,7}`

Therefore:

`J(B,C) = 1/6 = 0.167`

### Answer

| Pair | Jaccard Similarity |
| ---- | ------------------ |
| A, B | `0.333`            |
| A, C | `0.400`            |
| B, C | `0.167`            |

Therefore:

**A and C are the most similar.**

---

# Q2. Compute Jaccard Similarity with Repeated Elements

Given:

`A = {1,1,1,2}`

`B = {1,1,2,2,3}`

`C = {1,2,3,4}`

### Important

Jaccard Similarity works with **sets**, so repeated elements are ignored.

Therefore:

`A = {1,2}`

`B = {1,2,3}`

`C = {1,2,3,4}`

### A and B

Intersection:

`A ∩ B = {1,2}`

Union:

`A ∪ B = {1,2,3}`

Therefore:

`J(A,B) = 2/3 = 0.667`

### A and C

Intersection:

`A ∩ C = {1,2}`

Union:

`A ∪ C = {1,2,3,4}`

Therefore:

`J(A,C) = 2/4 = 0.5`

### B and C

Intersection:

`B ∩ C = {1,2,3}`

Union:

`B ∪ C = {1,2,3,4}`

Therefore:

`J(B,C) = 3/4 = 0.75`

### Answer

| Pair | Jaccard Similarity |
| ---- | ------------------ |
| A, B | `0.667`            |
| A, C | `0.500`            |
| B, C | `0.750`            |

Therefore:

**B and C are the most similar.**

---

## Important Point

### Jaccard ignores repetition

For example:

`A = {1,1,1,2}`

After converting to a set:

`A = {1,2}`

The three occurrences of `1` are treated as **one element**.

So:

> **Jaccard Similarity → Unique elements only → Repetitions are ignored.**

If we need to consider **how many times an element occurs**, then Jaccard Similarity is not suitable for that purpose.

---

### Shingling of Documents