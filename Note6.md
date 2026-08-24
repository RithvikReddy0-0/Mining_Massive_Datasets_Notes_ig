# Mining of Data Streams

## Overview

- **Data Stream** is a **continuous and rapid flow of data** from which we try to extract useful information.
- Data arrives **continuously and at high speed**, making it difficult for traditional databases to store all the data.
- Since stream data can be **very large and potentially unbounded**, storing the entire stream is often not feasible.
- If the stream data is not **processed immediately**, it may be lost or become unavailable.
- The main goal of **Data Stream Mining** is to **efficiently process, analyze, and extract useful patterns** from continuously arriving data.

> **Important:** Know the difference between **Ad-Hoc Queries** and **Standing (Stream) Queries** *(Fig 4.1)*.

---

## Stream Processing System

- A **Data Stream Management System (DSMS)** is used to **process and analyze continuously arriving data streams**.
- Since the incoming data can be very large and continuous, the system usually has **limited working storage** and uses **archival storage** for long-term data.

### Components of a Stream Processing System

1. **Incoming Stream**
   - The continuous flow of data entering the system.
   - Example: sensor data, stock prices, network traffic, etc.

2. **Ad-Hoc Queries**
   - Queries **issued by the user when needed**.
   - Usually one-time queries used to obtain specific information.
   - Example: **"What was the temperature at 2:00 PM?"**

3. **Stream Processor**
   - The **main processing unit** of the system.
   - Processes incoming streams and executes the queries.

4. **Limited Working Storage**
   - Temporary storage used by the stream processor.
   - Since streams can be very large, only a **limited amount of recent/relevant data** can be stored.

5. **Standing Queries**
   - Queries that **continuously run** on the incoming data stream.
   - They automatically process new data as it arrives.
   - Example: **"Alert me whenever the temperature exceeds 40°C."**

6. **Archival Storage**
   - Used to **store historical data** for long-term access and analysis.
   - Useful when older data needs to be queried later.

7. **Output Storage**
   - Stores the **results produced by the stream processor**.
   - The results can then be used by applications or users.

---

## Example: Temperature Monitoring

Suppose sensors continuously send temperature readings:

```
25°C → 26°C → 27°C → 29°C → 31°C → 35°C → ...
```

### Ad-Hoc Query
**"What was the temperature at 2:00 PM?"**
- The query is asked at a particular point in time.
- The system searches the available data to provide the answer.

### Standing Query
**"Alert me whenever temperature > 40°C."**
- The query keeps running continuously.
- Whenever a new temperature arrives, it is checked against the condition.

---

## Challenges in Data Stream Processing

Since data streams are continuous, large, and fast, processing them creates several challenges.

1. **Large Data Volume**
   - The amount of data can become extremely large.
   - It is not practical to store the entire stream.

2. **High Velocity**
   - Data can arrive at a very high speed.
   - The system needs to process data quickly enough to keep up with the incoming stream.

3. **Limited Memory**
   - The system has limited working storage.
   - Therefore, we cannot keep all the incoming data in memory.

4. **Latency**
   - Data often needs to be processed in real time or with very low delay.
   - High latency can make the result less useful.

5. **Complexity**
   - Continuously processing an unbounded stream is more complex than processing a fixed dataset.
   - The system has to continuously update its results as new data arrives.

6. **Data Loss**
   - If the incoming data is not processed quickly enough, it may pass through the system and become unavailable.
   - Therefore, efficient and fast processing is important.

7. **Changing Data**
   - The characteristics of the data may change over time.
   - The system should be able to adapt to these changes.

---

## Handling Data Stream Challenges

Since we cannot store and process the entire stream, we need techniques that reduce the amount of data being processed. Some important techniques are:

- Sampling
- Windowing
- Filtering
- Approximate Query Processing
- Data Summarization

### Sampling

Sampling means selecting a small representative subset from the incoming data stream. Instead of processing every data item, we process only the selected samples. This helps to reduce memory usage and processing time.

**Example:**

Suppose a stream contains:

```
1, 2, 3, 4, 5, 6, 7, 8, 9, 10, ...
```

Instead of processing every value, we may select:

```
2, 5, 8, 10, ...
```

The selected data is used to estimate or understand the characteristics of the complete stream.

**Main idea:**
```
Huge stream → Select representative data → Process less data
```

### Windowing

A window contains only a specific portion of the data stream. Instead of considering the entire history, we focus on a recent or relevant part of the stream.

**Example:**

For a temperature stream:

```
10:00 → 25°C
10:01 → 26°C
10:02 → 27°C
10:03 → 28°C
10:04 → 30°C
...
```

We can define a 5-minute window and process only the most recent 5 minutes of data.

#### Types of Windows

- **Sliding Window**
  - Moves continuously as new data arrives.
  - Old data is removed as new data enters.

- **Tumbling Window**
  - Divides the stream into non-overlapping fixed-size windows.

**Main idea:**
```
Instead of processing everything → process only a relevant window of data.
```

### Filtering

Filtering removes data that is not relevant to the current task. Only data satisfying a particular condition is processed further.

**Example:**

Temperature Stream:
```
25, 31, 28, 42, 29, 45, 33
```

Query:
```
Temperature > 40°C
```

Output:
```
42, 45
```

This reduces the amount of data that needs to be processed.

### Approximate Query Processing

In some cases, obtaining an exact answer from a massive data stream may be too expensive. Instead, we can calculate an approximate answer using a smaller amount of data. The goal is to obtain a result that is close enough to the exact answer while using less memory and computation.

**Example:**

Instead of calculating the exact average temperature from millions of readings, we can use a representative sample to estimate the average.

### Data Summarization

Instead of storing every individual data item, we store a compact summary of the data. The summary should preserve the important characteristics of the stream. This helps reduce storage and processing requirements.

---

## Key Concept

```
Continuous + Huge + Fast Data
            ↓
   Cannot store everything
            ↓
   Cannot process everything
            ↓
     Need efficient techniques
            ↓
 Sampling / Windowing / Filtering
 Approximation / Summarization
```

---

## Summary

**Data Stream Mining** = Process continuously arriving data efficiently using limited memory and time.