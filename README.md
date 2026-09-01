# MMDS — Mining of Massive Datasets Notes

Class notes for **Mining of Massive Datasets (MMDS)**, written in Markdown. Each file covers one topic in the order it was taught, with definitions, worked examples, and exam-oriented points.

## Contents

| File | Topic | Covers |
|------|-------|--------|
| [note3.md](note3.md) | Evaluation & MapReduce Basics | Evaluation components (report, presentation, viva, PySpark/ML in endsems), MapReduce for matrix multiplication, the 2nd MR job, worked 2×2 example with triples |
| [note4.md](note4.md) | Map Function | Matrix multiplication via MapReduce, `P[i][k] = Σ M[i][j] × N[j][k]`, mapper key-value design, Map → Shuffle/Sort → Reduce pipeline |
| [Note5.md](Note5.md) | Locality Sensitive Hashing | Why LSH (avoiding `O(n²)` comparisons), bucketing, Jaccard similarity, collaborative filtering as a similar-sets problem, solved similarity questions |
| [Note6.md](Note6.md) | Mining of Data Streams | Data streams, DSMS architecture, ad-hoc vs standing queries, stream processor, limited working vs archival storage, challenges and how they are handled |
| [Note7.md](Note7.md) | Stream Sampling & Tuples | Dividing streams into tuples, stream classes, primary keys and sampling units, hash-based sampling with thresholds, DGMI buckets, frequency estimation |
| [Note8.md](Note8.md) | Link Analysis | PageRank, web as a directed graph, random surfer model, transition matrix, damping factor, iteration, dangling nodes, spider traps, rank sink, term and link spamming |

## Topic Map

- **MapReduce** — `note3.md`, `note4.md`
- **Similarity & LSH** — `Note5.md`
- **Data Streams** — `Note6.md`, `Note7.md`
- **Link Analysis / PageRank** — `Note8.md`

## How to Use

- Read the files in numeric order — they follow the sequence of the lectures.
- Formulas are written in plain text/code blocks so they render correctly on GitHub without a math extension.
- Worked examples appear right after the theory for each concept; solve them yourself before reading the answer.
- Sections marked as key points or summaries are the ones worth revising right before an exam.

## Notes

- All notes are plain Markdown — no build step, no dependencies.
- Contributions are corrections and additions to existing notes; keep the heading style and example-after-theory structure consistent.

## Slides & Handouts

The [Class PPts/](Class%20PPts/) folder holds the original course material the notes were made from:

- **Slides** — `1-Introduction.pptx`, `2-MapReduce.pptx`, `3-LSH.pptx`, `4-Streams1.pptx`, `4-Streams2.pptx`, `5-Linkanalysis1.pptx`, `5-Linkanalysis2.pptx`
- **Chapter handouts** — Applications of Set Similarity (Ch. 3), Stream Data Model (Ch. 4), Apriori (Ch. 6)
- **Worked examples** — `LSH_Minhash_Complete_Example.docx`, `LinkSpam-MMDS-Exercises.docx`, `PageRankAlgorithm-Worked Example.pdf`
