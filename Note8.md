# Link Analysis

* **Link Analysis** is used to analyze the **relationships/links between web pages**.
* It helps determine which web pages are **important, popular, or authoritative** based on how they are connected.

---

## PageRank

* **PageRank** is an algorithm used by search engines to **rank web pages** based on their importance.
* It assigns a **real number (score)** to each web page.
* A page gets a higher PageRank when **important pages link to it**.

### Important Terms

* **Surfer** → The person who is browsing/visiting different web pages.
* **Web Page** → A page on the Internet.
* **Link/Hyperlink** → A connection from one web page to another.

---

## Web as a Graph

The web can be represented as a **directed graph**:

* **Nodes** → Web pages
* **Edges** → Links between web pages

```text
Page A ─────→ Page B
  │             │
  ↓             ↓
Page C ─────→ Page D
```

* If **Page A links to Page B**, Page A gives some importance/recommendation to Page B.
* If many important pages link to **Page B**, Page B receives a **higher PageRank**.

---

## Basic Idea of PageRank

> **A page is important if it is linked to by other important pages.**

* PageRank does **not simply count the number of incoming links**.
* The **importance of the pages providing the links** also matters.

### Example

```text
Page A ──→ Page C
Page B ──→ Page C
Page D ──→ Page C
```

* Page C has **3 incoming links**.
* Therefore, Page C may have a high PageRank.

However:

```text
Important Page ──→ Page C
```

* One link from a **highly important page** can be more valuable than many links from unimportant pages.

---

# Surfer Model

* PageRank can be understood using the idea of a **random surfer**.
* Imagine a surfer randomly browsing the web by clicking links.
* A page has a higher PageRank if the surfer is **more likely to visit that page**.

### Random Surfer

```text
Page A → Page B → Page C
  ↑                 ↓
  └─────────────────┘
```

* The surfer moves from one page to another by following hyperlinks.
* Over time, some pages are visited more frequently.
* These pages receive a **higher PageRank score**.

---

## PageRank Probability Matrix

Consider four pages:

```text
A → B, C, D
B → A, D
C → A
D → B
```

### Outgoing Links

| Page | Outgoing Links | Number of Outgoing Links |
| ---- | -------------- | -----------------------: |
| A    | B, C, D        |                        3 |
| B    | A, D           |                        2 |
| C    | A              |                        1 |
| D    | B              |                        1 |

The surfer chooses one of the outgoing links **with equal probability**.

Therefore:

* From **A**:

  * A → B = `1/3`
  * A → C = `1/3`
  * A → D = `1/3`

* From **B**:

  * B → A = `1/2`
  * B → D = `1/2`

* From **C**:

  * C → A = `1`

* From **D**:

  * D → B = `1`

### Transition Probability Matrix

Using **rows = source page** and **columns = destination page**:

| From \ To |   A |   B |   C |   D |
| --------- | --: | --: | --: | --: |
| **A**     |   0 | 1/3 | 1/3 | 1/3 |
| **B**     | 1/2 |   0 |   0 | 1/2 |
| **C**     |   1 |   0 |   0 |   0 |
| **D**     |   0 |   1 |   0 |   0 |

```text
       To
       A    B    C    D
From
A      0   1/3  1/3  1/3
B     1/2   0    0   1/2
C      1    0    0    0
D      0    1    0    0
```

### Important

Each row represents the probability of moving **from one page to another**.

The sum of every row is:

```text
1
```

For example:

```text
A → B = 1/3
A → C = 1/3
A → D = 1/3

Total = 1/3 + 1/3 + 1/3 = 1
```

---

# PageRank Formula

The basic PageRank equation is:

[
PR(A) = \sum_{T_i \in In(A)} \frac{PR(T_i)}{C(T_i)}
]

Where:

* `PR(A)` → PageRank of page A
* `T_i` → A page that links to A
* `PR(T_i)` → PageRank of the linking page
* `C(T_i)` → Number of outgoing links from `T_i`

### Meaning

> A page receives a portion of the PageRank of every page that links to it.

If a page has many outgoing links, its PageRank is **divided among those links**.

### Example

Suppose:

```text
A → C
B → C
```

and:

```text
PR(A) = 0.6
PR(B) = 0.4
```

If both A and B have only one outgoing link:

```text
PR(C) = 0.6/1 + 0.4/1
      = 1.0
```

So C receives the complete contribution from both pages.

---

# Damping Factor

The basic PageRank model has a problem: the surfer may get stuck following links forever.

To solve this, PageRank uses a **damping factor**.

Usually:

[
d = 0.85
]

The PageRank formula becomes:

[
PR(A) =
\frac{1-d}{N}
+
d\sum_{T_i \in In(A)}
\frac{PR(T_i)}{C(T_i)}
]

Where:

* `d` → Damping factor, commonly `0.85`
* `N` → Total number of web pages
* `(1-d)/N` → Probability of randomly jumping to any page

### Random Surfer Interpretation

At every step, the surfer:

* With probability **0.85** → follows a link.
* With probability **0.15** → jumps to a random web page.

```text
             Random Surfer
                  │
        ┌─────────┴─────────┐
        ↓                   ↓
   Follow Link          Random Jump
      85%                   15%
```

### Why is damping needed?

It helps:

* Avoid getting stuck in a cycle.
* Handle pages with no outgoing links.
* Make PageRank stable.
* Ensure the surfer can eventually reach different parts of the web.

---

# PageRank Iteration

PageRank is calculated **iteratively**.

### General Process

```text
Initial PageRank
       ↓
Calculate new PageRank
       ↓
Calculate again
       ↓
Calculate again
       ↓
...
       ↓
Convergence
       ↓
Final PageRank
```

Initially, every page can be given an equal PageRank:

[
PR(A)=PR(B)=...=PR(N)=\frac{1}{N}
]

Then the values are repeatedly updated until they become almost stable.

### Convergence

When the PageRank values change only by a very small amount between iterations, the algorithm is considered to have **converged**.

---

# Dangling Nodes

A **dangling node** is a web page that has **no outgoing links**.

Example:

```text
A → B → C

C → nothing
```

Here, C is a **dangling node**.

### Problem

A random surfer reaching C has nowhere to go by following a link.

### Solution

The PageRank algorithm treats the dangling page as if its probability is distributed among **all pages**, typically together with the random-jump mechanism.

---

# Spider Traps

A **spider trap** is a group of pages that link only to pages inside the same group.

Example:

```text
A → B
↑   ↓
└───C
```

The surfer can enter this group and continue moving inside it without leaving.

### Problem

The surfer may become trapped inside the group.

### Solution

The **damping factor** allows the surfer to randomly jump outside the trap.

---

# Rank Sink

A **rank sink** is a situation where PageRank becomes concentrated in a particular group of pages because there are insufficient links leading out of that group.

### Remember

* **Spider Trap** → Group of pages traps the surfer.
* **Rank Sink** → PageRank becomes concentrated in a particular region.

The damping factor helps prevent these problems.

---

# Term Spamming

* **Term Spamming** is a technique used to **artificially increase the relevance/ranking of a web page** by repeatedly using popular or relevant keywords.
* The goal is to make a page appear more relevant to a search engine.

### Example

Suppose a website sells laptops.

```text
Laptop Laptop Laptop

Best Laptop

Cheap Laptop

Laptop Laptop

Buy Laptop

Laptop Laptop Laptop
```

* The word **"Laptop"** is unnecessarily repeated.
* This attempts to make the search engine consider the page highly relevant for **"Laptop"**.
* This is called **Term Spamming**.

---

# Link Spamming

* **Link Spamming** is the practice of creating **artificial or excessive links** to a web page to increase its ranking.
* It attempts to manipulate link-based ranking algorithms such as PageRank.

### Example

```text
Page A ──→ Target Page
Page B ──→ Target Page
Page C ──→ Target Page
Page D ──→ Target Page
Page E ──→ Target Page
```

* If these links are created artificially just to increase the Target Page's ranking, it is **Link Spamming**.

---

# Types of Web Spamming

## 1. Term Spamming

* Manipulates the **content/keywords** of a page.
* Example: Repeating keywords unnecessarily.

```text
"Car Car Car Car Car Car..."
```

**Memory Trick:**

> **Term = Words**

---

## 2. Link Spamming

* Manipulates the **links pointing to a page**.
* Example: Creating many artificial links to increase PageRank.

```text
A ──→ Target
B ──→ Target
C ──→ Target
D ──→ Target
```

**Memory Trick:**

> **Link = Connections**

---

# PageRank vs Term Spamming vs Link Spamming

| Concept           | Main Idea                            | Manipulates   |
| ----------------- | ------------------------------------ | ------------- |
| **PageRank**      | Ranks pages based on link importance | Ranking       |
| **Term Spamming** | Repeats keywords                     | Content/Words |
| **Link Spamming** | Creates artificial links             | Links         |
| **Random Surfer** | Models how users browse pages        | User movement |

---

# Link Analysis vs PageRank

### Link Analysis

A **general technique** for studying relationships between web pages.

```text
Web Pages
   ↓
Links
   ↓
Graph
   ↓
Analyze relationships
```

### PageRank

A **specific algorithm** that uses link relationships to calculate the importance of web pages.

```text
Links
  ↓
PageRank Algorithm
  ↓
Importance Score
  ↓
Ranking
```

### Easy Way to Remember

> **Link Analysis = Study the links**

> **PageRank = Use the links to rank pages**

---

# Important PageRank Concepts

| Term               | Meaning                              |
| ------------------ | ------------------------------------ |
| **Node**           | Web page                             |
| **Edge**           | Hyperlink                            |
| **Incoming Link**  | Link coming into a page              |
| **Outgoing Link**  | Link going out of a page             |
| **PageRank**       | Importance score of a page           |
| **Surfer**         | Person browsing the web              |
| **Random Surfer**  | Mathematical model of web browsing   |
| **Damping Factor** | Probability of following a link      |
| **Dangling Node**  | Page with no outgoing links          |
| **Spider Trap**    | Group of pages that traps the surfer |
| **Term Spamming**  | Manipulating keywords                |
| **Link Spamming**  | Manipulating links                   |

---

# Easy Way to Remember

* **PageRank → Rank pages based on links**
* **Term Spamming → Spam the words**
* **Link Spamming → Spam the links**
* **Surfer → Person browsing the web**
* **Web Page → Node**
* **Hyperlink → Edge**
* **Incoming Link → Link coming IN**
* **Outgoing Link → Link going OUT**
* **Dangling Node → No OUTgoing links**
* **Damping → Random Jump**
* **Spider Trap → Surfer gets trapped**

---

# Key Points for Exams

1. **PageRank assigns a numerical importance score to web pages.**
2. A page is important if it is linked by **other important pages**.
3. The web can be represented as a **directed graph**.
4. **Nodes = web pages**.
5. **Edges = hyperlinks**.
6. PageRank is based on the **random surfer model**.
7. The commonly used damping factor is **0.85**.
8. About **85%** of the time, the surfer follows a link.
9. About **15%** of the time, the surfer makes a random jump.
10. A **dangling node** has no outgoing links.
11. A **spider trap** can trap a surfer in a group of pages.
12. **Term spamming** manipulates words/keywords.
13. **Link spamming** manipulates hyperlinks.
14. PageRank is calculated **iteratively** until the values converge.

---

# One-Line Summary

> **PageRank = Importance of a web page based on the importance of pages linking to it.**

> **Link Analysis = Analyze relationships between web pages.**

> **Term Spamming = Manipulate words.**

> **Link Spamming = Manipulate links.**

> **Random Surfer = Model a user randomly browsing the web.**
