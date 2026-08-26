# Spam Mass and TrustRank: Detecting Web Spam

## Spam Mass

### Definition

**Spam Mass** identifies whether a web page is **suspicious or potentially involved in spam** by comparing its **PageRank** (importance) with its **TrustRank** (trustworthiness).

### Formula

$$\text{Spam Mass} = \frac{\text{PageRank} - \text{TrustRank}}{\text{PageRank}} = 1 - \frac{\text{TrustRank}}{\text{PageRank}}$$

### Interpretation

| Spam Mass Value | Meaning |
|-----------------|---------|
| **> 0** | PageRank > TrustRank → **Potentially Suspicious** |
| **= 0** | PageRank = TrustRank → **Neutral** |
| **< 0** | TrustRank > PageRank → **Trustworthy** |

> **Higher positive Spam Mass = Higher suspicion of spam**

---

### Example: Comparing Pages

| Node | PageRank | TrustRank | Spam Mass | Status |
|------|----------|-----------|-----------|--------|
| A | 3/9 = 0.333 | 54/210 = 0.257 | (0.333 - 0.257)/0.333 = **0.229** | ⚠️ Suspicious |
| B | 2/9 = 0.222 | 59/210 = 0.281 | (0.222 - 0.281)/0.222 = **-0.265** | ✓ Trustworthy |
| C | 2/9 = 0.222 | 38/210 = 0.181 | (0.222 - 0.181)/0.222 = **0.186** | ⚠️ Suspicious |

**Interpretation:**
- **Node A**: Popular (0.333) but less trusted (0.257) → **22.9% spam risk**
- **Node B**: Less popular (0.222) but more trusted (0.281) → **Trustworthy despite lower popularity**
- **Node C**: Popular but untrusted → **18.6% spam risk**

---

## TrustRank Calculation

### Concept

TrustRank starts from **seed sites** (known trusted sources) and propagates trust through hyperlinks. Trust decreases as it flows through the network.

### TrustRank Formula

For any page X that receives links from seed sites:

$$\text{TrustRank}(X) = \frac{\text{TR}(A) + \text{TR}(B) + ... + \text{TR}(N)}{\text{Number of Seed Sites}}$$

Where:
- A, B, ..., N are pages linking to X
- Number of Seed Sites = total seed sites in the network

### Algorithm

1. **Initialize seed sites** with TrustRank = 1.0
2. **For each page**: Sum TrustRank values of all incoming links
3. **Divide by number of seed sites** to get normalized TrustRank
4. **Repeat** until convergence

### Example: Trust Propagation Network

**Network Structure:**

```
        A (Seed)          B (Seed)
         |                 |
         └────→ X ←────────┘
                 |
            ┌────┴────┐
            |         |
            Y         M
            |
        ┌───┴───┐
        |       |
        Z       Q
```

- **A and B** are seed sites (trusted = 1.0)
- **A links to X** and **B links to X**
- **X links to Y and M**
- **Y links to Z and Q**

### Calculation Steps

**Step 1: Initialize Seed Sites**

```
TR(A) = 1.0 (seed)
TR(B) = 1.0 (seed)
TR(X) = 0 (initially)
TR(Y) = 0
TR(M) = 0
TR(Z) = 0
TR(Q) = 0

Number of Seed Sites = 2
```

**Step 2: Iteration 1 - Calculate TR(X) from A and B**

```
A links to X with TR(A) = 1.0
B links to X with TR(B) = 1.0

TR(X) = (TR(A) + TR(B)) / Number of Seeds
TR(X) = (1.0 + 1.0) / 2 = 1.0
```

**Step 3: Iteration 2 - Calculate TR(Y) and TR(M) from X**

```
X links to Y and M, distributing its trust equally:
Trust per link = TR(X) / 2 = 1.0 / 2 = 0.5

Only X contributes to Y and M (not seed sites):
TR(Y) = 0.5
TR(M) = 0.5
```

**Step 4: Iteration 3 - Calculate TR(Z) and TR(Q) from Y**

```
Y links to Z and Q, distributing its trust equally:
Trust per link = TR(Y) / 2 = 0.5 / 2 = 0.25

Only Y contributes to Z and Q:
TR(Z) = 0.25
TR(Q) = 0.25
```

**Step 5: Continue until Convergence**

```
All downstream pages receive proportional trust decreasing with distance.
```

**Final TrustRank Values (Converged):**

| Node | TrustRank | Calculation | Trust Distance |
|------|-----------|-------------|-----------------|
| **A** | 1.0 | Seed (direct) | 0 hops |
| **B** | 1.0 | Seed (direct) | 0 hops |
| **X** | 1.0 | (1.0 + 1.0) / 2 | 1 hop |
| **Y** | 0.5 | 1.0 / 2 | 2 hops |
| **M** | 0.5 | 1.0 / 2 | 2 hops |
| **Z** | 0.25 | 0.5 / 2 | 3 hops |
| **Q** | 0.25 | 0.5 / 2 | 3 hops |

### Key Observations

1. **Seed sites** have trust = 1.0 (by definition)
2. **Pages linked by multiple seeds** average their trust values
3. **Trust is divided** equally among outgoing links
4. **Trust decreases** with each hop from seed sites
5. **Pages far from seeds** have lower trust values
6. **Formula averages trust** from all seed sites (not accumulative)

---

## Combining Spam Mass with TrustRank

Once TrustRank is calculated, you can compute Spam Mass:

### Example: Computing Spam Mass

```
Page X:
- PageRank = 0.6 (popular from multiple sources)
- TrustRank = 1.0 (average of seed sites A and B)

Spam Mass = (0.6 - 1.0) / 0.6 = -0.4 / 0.6 = -0.667

Interpretation: X is TRUSTWORTHY
(Receives trust from both seed sites, negative spam mass)

---

Page M (potentially spam):
- PageRank = 0.7 (artificially boosted through link spam)
- TrustRank = 0.5 (only reached through X, 2 hops from seeds)

Spam Mass = (0.7 - 0.5) / 0.7 = 0.2 / 0.7 = 0.286

Interpretation: M is SUSPICIOUS
(Higher PageRank than TrustRank suggests, 28.6% spam risk)

---

Page Z (deep page):
- PageRank = 0.3 (low popularity)
- TrustRank = 0.25 (3 hops from seed sites)

Spam Mass = (0.3 - 0.25) / 0.3 = 0.05 / 0.3 = 0.167

Interpretation: Z is SLIGHTLY SUSPICIOUS
(16.7% spam risk, but limited popularity limits harm)
```

---

## Link Spam Detection

### What is Link Spam?

**Link Spam** is the practice of creating artificial links to artificially boost a page's PageRank without gaining legitimate trust. Spammers use various techniques to manipulate link structure and deceive search engines.

### Link Spam Patterns

#### Pattern 1: High PageRank, Low TrustRank

A page with many incoming links (high PageRank) but little trust from seed sites (low TrustRank) is a strong indicator of link spam.

**Example: Page Z Analysis**

```
Network Structure:

Seed Sites (A, B) with TR = 1.0
         ↓
Trusted Pages (X, Y) with TR = 0.5, 0.5
         ↓
Suspicious Page (Z)

BUT Page Z receives links from MANY EXTERNAL WEBSITES:

Z ← Website1 (no trust connection)
Z ← Website2 (no trust connection)
Z ← Website3 (no trust connection)
Z ← Website4 (no trust connection)
... (hundreds of low-quality links)
Z ← Website100 (no trust connection)
```

**Characteristics:**

```
Page Z Statistics:
- PageRank = 0.8 (high, due to many artificial links)
- TrustRank = 0.25 (very low, far from trusted seeds)
- Number of Inbound Links = 100+ (mostly from unrelated sites)
- Link Quality = Poor (from low-authority sites)

Spam Mass Calculation:
Spam Mass = (0.8 - 0.25) / 0.8 = 0.55 / 0.8 = 0.6875

Result: 68.75% SPAM RISK - HIGHLY SUSPICIOUS!
```

**Why It's Spam:**

1. **Disproportionate metrics**: PageRank >> TrustRank
2. **Link farm pattern**: Many links from unrelated websites
3. **No trust path**: No connection to legitimate seed sites
4. **Artificial boost**: Links don't reflect natural recommendation

---

### How Spam Mass Detects Link Spam

**The Detection Logic:**

| Scenario | PageRank | TrustRank | Spam Mass | Reason |
|----------|----------|-----------|-----------|--------|
| **Legitimate site** | 0.6 | 0.6 | 0.0 | Trust matches popularity |
| **Popular & trusted** | 0.7 | 0.75 | -0.07 | More trusted than popular (good) |
| **Link spam** | 0.8 | 0.25 | 0.69 | High PR from artificial links, low trust |
| **Authority page** | 0.9 | 0.85 | 0.06 | Both high, slight boost from spam |

---

### Real-World Example: Link Spam Farm

**Scenario: E-commerce Spam Site**

```
A spam site ("cheapproducts-fake.com") wants to rank for "designer handbags"

Step 1: Create Hundreds of Link Farm Websites
- blogspam1.com (5 links to target)
- blogspam2.com (5 links to target)
- forumspam1.com (10 links to target)
- forumspam2.com (10 links to target)
... 50 spam sites total with 300 total links

Step 2: Check Metrics

Legitimate Site (realdesigner.com):
- PageRank = 0.7
- TrustRank = 0.65 (from luxury brand seed sites)
- Inbound links = 50 (from reputable fashion sites)
- Spam Mass = (0.7 - 0.65) / 0.7 = 0.071 (7.1% - LOW RISK)

Spam Site (cheapproducts-fake.com):
- PageRank = 0.75 (boosted by 300 artificial links)
- TrustRank = 0.15 (none of the spam sites are trusted)
- Inbound links = 300+ (from low-quality spam sites)
- Spam Mass = (0.75 - 0.15) / 0.75 = 0.8 (80% - VERY SUSPICIOUS!)

Step 3: Detection Result
Despite similar PageRank, spam site is detected due to:
- Massive Spam Mass difference (0.8 vs 0.071)
- Links from non-authoritative sources
- No trust path from legitimate seeds
- Unnatural link acquisition pattern
```

---

### Types of Link Spam Detected

#### Type 1: Private Link Networks (PBN)

```
Attacker controls multiple websites that all link to target:

Domain1 → Target
Domain2 → Target
Domain3 → Target
...
Domain50 → Target

Spam Mass reveals: High PageRank but low TrustRank
(Links don't come from trusted sources)
```

#### Type 2: Reciprocal Linking Schemes

```
Site A links to Site B
Site B links to Site A
(Artificial mutual boosting)

Detection: Both sites show disproportionate PR vs TR
```

#### Type 3: Blog Comment Spam

```
Spam Site leaves comments on thousands of blogs:

Blog1: "Great article! Visit myspamsite.com"
Blog2: "This is helpful, myspamsite.com has more"
...
Blog1000: "Nice post, myspamsite.com"

Result: Low-quality links boost PageRank
TrustRank stays low (blog comments aren't trusted)
```

#### Type 4: Forum Profile Links

```
Attacker creates 500+ forum profiles with links in signatures:

Forum1 profile → Link to spam site
Forum2 profile → Link to spam site
...
Forum500 profile → Link to spam site

Spam Mass detects: PageRank boost without trust growth
```

---

## Complete Detection Strategy

### Spam Mass Thresholds

Use these thresholds to classify pages:

```
Spam Mass ≤ 0.05      → GREEN ZONE (Legitimate page)
0.05 < Spam Mass ≤ 0.2 → YELLOW ZONE (Caution - Review manually)
0.2 < Spam Mass ≤ 0.5  → ORANGE ZONE (Likely spam - Low priority)
Spam Mass > 0.5        → RED ZONE (Highly suspicious spam - Penalize)
```

### Actionable Workflow

1. **Calculate PageRank** for all pages
2. **Calculate TrustRank** from trusted seeds
3. **Compute Spam Mass** for each page
4. **Flag suspicious pages** (Spam Mass > 0.2)
5. **Investigate link sources** for flagged pages
6. **Take action**:
   - Remove from index (Spam Mass > 0.5)
   - Reduce ranking (0.2 < Spam Mass ≤ 0.5)
   - Monitor (0.05 < Spam Mass ≤ 0.2)

---

## Comparison Table: Legitimate vs Spam

| Factor | Legitimate Site | Spam Site |
|--------|-----------------|-----------|
| **PageRank** | High | High (artificial) |
| **TrustRank** | High | Low |
| **Spam Mass** | Low (≈ 0) | High (> 0.3) |
| **Link Sources** | Reputable domains | Low-quality domains |
| **Link Pattern** | Natural growth | Sudden spikes |
| **Seed Proximity** | Close (few hops) | Distant (many hops) |
| **Link Quality** | Contextual links | Irrelevant links |

---

## Summary

| Concept | Purpose |
|---------|---------|
| **TrustRank** | Measure trustworthiness based on distance from trusted seeds |
| **Spam Mass** | Detect spam by comparing PageRank vs TrustRank |
| **Seed Sites** | Known trusted sources (Wikipedia, universities, news sites) |
| **Link Spam** | Artificial links created to manipulate PageRank |
| **Negative Spam Mass** | Indicates a trustworthy page (TrustRank > PageRank) |
| **Positive Spam Mass** | Indicates potentially spammy page (PageRank > TrustRank) |
| **High Spam Mass** | Strong signal of link spam activity |

### Key Insight

**PageRank alone is not sufficient** for ranking. By comparing PageRank with TrustRank, search engines can:
- Detect artificial link manipulation
- Identify link spam networks
- Distinguish legitimate popularity from spam tactics
- Maintain quality of search results

Spam Mass = **The gap between what PageRank says and what TrustRank confirms.**