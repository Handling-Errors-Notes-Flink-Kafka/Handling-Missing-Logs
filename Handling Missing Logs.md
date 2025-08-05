# Handling Missing Logs: Searching Events in Kafka UI

## 🧩 Problem

When working with big data systems, sometimes user or stakeholder asks you to check specific logs or events. Normally, we just go to Graylog (or other log systems) to search it.

But sometimes life is not that easy:
- The logs were **not forwarded** to Graylog (due to config issue, or logging is skipped).
- Or logs are already **expired** (because retention is short, or the index is full).

Luckily, in some cases, data is still inside **Kafka** – the raw stream layer.

So today, I want to share how I manually searched log events in Kafka UI to handle this kind of situation.

---

## 🔎 What Happened

- Graylog didn’t receive the log (log forwarding was missing).
- Or data was removed from Graylog due to timeout or retention policy.
- Fortunately, the Kafka topic still stored the raw events.
- So I had to go into **Kafka UI**, and search the data manually.

---

## 🛠️ My Approach

### 1. Open Kafka UI
- Choose the cluster
- Choose the topic

### 2. Identify the time range

Because when you deal with big data, **volume is huge**, and you can’t just scroll randomly.

So first, I try to estimate when the event happened:
- Get the **min and max timestamp** of the event time.
- You can check from user reports, related service logs, or monitoring alerts.

### 3. Configure seek options

In Kafka UI:
- Set `seek type = timestamp`.
- Set time = estimated time (start time).
- Choose direction:
  - `oldest first` if you're reading forward.
  - `newest first` if you're reading backward.

This helps you **scan only a small time window**, not full topic.

### 4. Use Groovy filter (optional but helpful)

Sometimes string search is not enough. You can add **Groovy-based filter** to narrow down the result.

You can also **save common filters** for reuse.

Example:

```groovy
value.name == "iS.ListItemax" && value.age > 30
