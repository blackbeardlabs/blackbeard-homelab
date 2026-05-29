# Response: Qwen3.6 Heretic BF16 prompt-02 scheduler on node-04-v1 Kraken

## Metadata

| Field | Value |
|---|---|
| Date | 2026-05-29 |
| Node | node-04-v1 (Kraken) |
| Model | llmfan46/Qwen3.6-27B-uncensored-heretic-v2-Native-MTP-Preserved |
| Backend | vLLM 0.21.0 |
| Prompt id | `prompts/prompt-02-heterogeneous-scheduler.md` |
| Request method | OpenWebUI |
| Sampling params | OpenWebUI defaults; exact sampling parameters not explicitly captured |
| Benchmark | `benchmarks/node-04-kraken-4x3090/qwen36-27b-heretic-bf16-vllm-260k-mtp2.md` |
| Raw log | `raw-logs/2026-05-29-node-04-qwen36-heretic-prompt-02-scheduler.log` |

## Human notes

- The response provides a practical greedy priority scheduler with affinity scoring.
- It covers scheduling factors, tradeoffs, failure cases, and pseudocode.
- Caveat: the job priority formula in the explanation appears sign-confused as written: subtracting `(ArrivalTime - Now)` works out as adding wait time if `ArrivalTime - Now` is negative, but `wait_time` would be clearer.
- Caveat: the pseudocode peeks only the highest-priority job. If that job has no feasible candidates, lower-priority jobs that could run may be blocked unless the implementation scans further into the queue.

## Model response

## Strategy: Priority-Based Best-Fit with Heterogeneity Affinity

For a small, heterogeneous cluster with an exclusive "one-model-per-node" constraint, a complex global optimizer is overkill. The most practical and efficient approach is a **Greedy Priority Scheduler with Affinity Scoring**.

This strategy assigns jobs based on **priority** (latency sensitivity and wait time) while selecting nodes based on a **scoring function** that maximizes resource match (affinity) and minimizes opportunity cost.

### 1. Core Components & Factors

#### Job Attributes

* **`mem_req`**: Memory required by the model/context.
* **`latency_sensitive`**: Boolean or weight (e.g., `high`, `low`). High for short, latency-sensitive jobs.
* **`context_size`**: Proxy for "heaviness." High for large models/long jobs.
* **`wait_time`**: Dynamic value increasing while in queue.

#### Node Attributes

* **`mem_cap`**: Available memory.
* **`speed_class`**: Enum `FAST` or `SLOW`.
* **`status`**: `IDLE` or `BUSY`.
* **`idle_time`**: Duration since node became idle (used to prevent starvation of resources).

#### Scheduling Factors

1. **Feasibility**: `job.mem_req <= node.mem_cap`.
2. **Affinity**:
   * *Short/Latency jobs* prefer `FAST` nodes.
   * *Large/Long jobs* prefer `SLOW` nodes (to preserve fast cycles).
3. **Opportunity Cost**: Penalize assigning Large jobs to Fast nodes if Slow nodes are available, to avoid blocking future Short jobs.
4. **Fairness**: Boost job priority based on `wait_time` to prevent starvation.

---

### 2. The Decision Framework

The scheduler operates in a continuous loop (or triggered by events). It maintains a **Priority Queue** of pending jobs.

#### Job Priority Queue

Jobs are sorted by an effective priority score. This ensures latency-sensitive jobs are considered first, but long jobs aren't starved.

```text
JobScore = (LatencyWeight * 100) - (ArrivalTime - Now)
```

* *LatencyWeight*: `1.0` for sensitive, `0.1` for others.
* As wait time increases, the job moves up the queue.

#### Node Selection Scoring

When the scheduler attempts to assign the head job, it scores all feasible idle nodes. The node with the highest score is selected.

```text
NodeScore = BaseFit + AffinityBonus - MismatchPenalty + IdleBonus
```

| Scenario | Affinity Logic | Score Impact |
| :--- | :--- | :--- |
| **Short Job -> Fast Node** | Perfect match for latency. | `+10` |
| **Large Job -> Slow Node** | Perfect match; saves Fast node. | `+8` |
| **Short Job -> Slow Node** | Acceptable, but higher latency. | `-5` |
| **Large Job -> Fast Node** | Wastes speed; blocks future short jobs. | `-4` |
| **Idle Bonus** | Prefer nodes idle longest to balance wear/util. | `+0.01 * idle_seconds` |

*Note: The Large->Fast penalty is "soft." If all Slow nodes are busy, the Fast node will still be selected because it beats the "No Node" state, but it is deprioritized relative to Slow nodes.*

---

### 3. Pseudocode

```python
class Scheduler:
    def __init__(self):
        self.nodes = []       # List of Node objects
        self.job_queue = PriorityQueue() # Sorted by JobScore descending

    def add_job(self, job):
        job.arrival_time = now()
        job.wait_time = 0
        self.job_queue.push(job)

    def tick(self):
        """Main scheduling loop, runs periodically or on events."""
        if self.job_queue.is_empty():
            return

        # 1. Pick the highest priority job
        job = self.job_queue.peek()

        # 2. Find all feasible nodes (Memory check & Status check)
        candidates = [
            n for n in self.nodes
            if n.status == 'IDLE' and n.mem_cap >= job.mem_req
        ]

        if not candidates:
            # No resources available, job waits in queue
            job.wait_time = now() - job.arrival_time
            return

        # 3. Score candidates and pick best
        best_node = None
        max_score = -float('inf')

        for node in candidates:
            score = self.score_node(node, job)
            if score > max_score:
                max_score = score
                best_node = node

        # 4. Assign
        self.job_queue.pop()
        self.assign(job, best_node)

    def score_node(self, node, job):
        score = 0

        # Base affinity logic
        if job.latency_sensitive and node.speed == 'FAST':
            score += 10      # Reward Fast for Latency
        elif not job.latency_sensitive and node.speed == 'SLOW':
            score += 8       # Reward Slow for Large/Long

        # Mismatch penalties
        if job.latency_sensitive and node.speed == 'SLOW':
            score -= 5       # Penalty: Latency will be higher
        elif not job.latency_sensitive and node.speed == 'FAST':
            score -= 4       # Penalty: Opportunity cost (wasting fast cycles)

        # Dynamic idle bonus (practical tie-breaker)
        score += node.idle_seconds * 0.01

        return score

    def assign(self, job, node):
        node.status = 'BUSY'
        node.current_job = job
        job.start_time = now()
        print(f"Scheduled {job.id} on {node.id} (Score: {self.score_node(node, job)})")
```

---

### 4. Tradeoffs and Failure Cases

#### Tradeoffs

1. **Latency vs. Throughput**:
   * *Tradeoff*: Prioritizing latency-sensitive jobs can increase the wait time for long jobs.
   * *Mitigation*: The `wait_time` boost in `JobScore` ensures long jobs eventually jump the queue if they wait too long.
2. **Simplicity vs. Optimality**:
   * *Tradeoff*: This greedy approach is `O(N * M)` per tick (where `N` is jobs, `M` is nodes). It does not look ahead. A burst of Large jobs might fill Slow nodes, forcing subsequent Short jobs to wait for Fast nodes even if Fast nodes are idle but "reserved" in logic.
   * *Mitigation*: The scoring penalties are soft. If Slow nodes are full, Fast nodes are still candidates. The algorithm naturally falls back to Fast nodes when Slow nodes are busy.
3. **Model Loading Overhead**:
   * *Tradeoff*: The scheduler assumes assignment is instantaneous. If loading models takes significant time, a Short job might miss its latency SLA due to load time on a Cold node.
   * *Mitigation*: In practice, add `node.load_time` to the score. For latency-sensitive jobs, prefer nodes where the model is already cached/warm, or add a `load_estimate` to the latency calculation.

#### Failure Cases

1. **Resource Starvation (Fast Node Starvation)**:
   * *Case*: If you set the penalty for `Large -> Fast` too high, and Slow nodes are occupied by Long jobs, Fast nodes might sit idle while Large jobs queue up, because the Large jobs prefer to wait for a Slow node rather than take a Fast one.
   * *Fix*: Ensure the scoring allows `Large -> Fast` to win over "Wait." In the pseudocode, this is handled by checking candidates first. As long as a Fast node is a candidate, it gets a score. The penalty only ranks it lower than a Slow node. It will still be picked if no Slow nodes exist.
2. **Memory Fragmentation**:
   * *Case*: Not applicable here due to the "one model per node" constraint. This simplifies the problem significantly compared to shared memory pools.
3. **Thrashing**:
   * *Case*: If jobs are extremely short and frequent, the scheduler might thrash assigning nodes.
   * *Fix*: Since nodes are exclusive, thrashing is low. However, ensure the scheduler tick rate is appropriate for the cluster size. For a small cluster, an event-driven approach (trigger on job submit or job finish) is better than polling.

### 5. Practical Implementation Tips

1. **Backpressure**: If the queue grows beyond a threshold, reject new jobs or kill low-priority waiting jobs to prevent memory bloat in the scheduler.
2. **Preemption Policy**:
   * Decide if running jobs can be preempted. If models take long to load, preemption is expensive.
   * *Recommendation*: Use **non-preemptive** scheduling with the `wait_time` boost. This is more stable for small clusters. Only preempt if a "Critical" job arrives and the only suitable node is busy with a "Low" job.
3. **Monitoring**:
   * Expose metrics: `queue_depth`, `avg_wait_time`, `node_utilization_by_speed`.
   * Adjust `AffinityBonus` weights dynamically based on cluster SLA violations.
4. **Health Checks**:
   * Remove nodes from `candidates` if health checks fail, not just based on memory.
   * Include a `drain` mode where nodes stop accepting new jobs and finish current work.

This strategy provides a robust, easy-to-debug, and efficient framework that respects the hardware heterogeneity while balancing the competing needs of latency and capacity.
