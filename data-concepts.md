In distributed systems, terms such as *availability*, *consistency*, *safety*, and *liveness* describe different kinds of guarantees. They are related, but not interchangeable. Using them precisely matters because design tradeoffs often depend on the exact guarantee being discussed.

### 1. Availability
- **Definition**: Availability means a system returns a non-error response to requests within a bounded time. In practice, users care that the service continues responding, even when some components fail.
- **Example**: A replicated API continues serving reads and writes while one node is down.
- **Trade-offs**: Systems often trade strict coordination for higher availability, especially across regions or during network failures.

### 2. Consistency
- **Definition**: "Consistency" is overloaded. In distributed systems, it usually refers to a read seeing an appropriate write according to a defined consistency model. It does **not** always mean every node is instantly identical.
- **Example**: A client writes a profile update and expects later reads to reflect that update according to the system's guarantees.
- **Trade-offs**: Stronger consistency models simplify reasoning but usually add coordination cost, latency, or reduced availability under partition.

### 3. Safety
- **Definition**: A safety property says something bad never happens.
- **Example**: Two leaders are never active for the same term in a consensus system.
- **Trade-offs**: Preserving safety may require rejecting requests or delaying progress during uncertain network conditions.

### 4. Liveness
- **Definition**: A liveness property says something good eventually happens.
- **Example**: A healthy consensus cluster eventually elects a leader and starts processing commands again.
- **Trade-offs**: A system can preserve safety by becoming conservative, but that may hurt liveness during failures or recovery.

### 5. Partition Tolerance
- **Definition**: Partition tolerance means the system is designed to continue operating despite network splits or lost communication between components.
- **Example**: Two regions temporarily lose connectivity, but each side still follows defined failure-handling behavior.
- **Trade-offs**: Once a partition exists, the system usually has to choose between stronger coordination and higher availability for some operations.

### 6. CAP Theorem
- **Definition**: CAP says that when a network partition occurs, a distributed system cannot simultaneously provide both full availability and strong consistency for the same operation.
- **Important Clarification**: CAP is about behavior during partitions, not a general three-way slider for every system design discussion.
- **Example**: During a partition, a system may reject writes to preserve a single consistent history, or accept writes and reconcile later.
- **Trade-offs**: Many real systems mix strategies by operation, data model, or product requirement.

### 7. Eventual Consistency
- **Definition**: Eventual consistency means that if updates stop, all replicas will converge to the same value after enough time.
- **Example**: A cached profile photo changes in one region and appears in another region a short time later.
- **Trade-offs**: This improves availability and performance, but clients may temporarily observe stale reads or conflicting versions.

### 8. Strong Consistency
- **Definition**: Strong consistency is an umbrella term and should be used carefully. In many conversations, people mean guarantees close to linearizability or at least immediate visibility of completed writes.
- **Example**: A balance update is visible to subsequent reads immediately after the write is acknowledged.
- **Trade-offs**: Stronger guarantees usually require synchronous coordination and can increase latency.

### 9. Linearizability
- **Definition**: Linearizability is a specific consistency model where each operation appears to take effect atomically at a single instant between request and response.
- **Example**: If one client successfully writes `x = 1`, a later read cannot return the old value.
- **Trade-offs**: Linearizability is often expensive across distant replicas because it requires tight coordination.

### 10. Sequential Consistency
- **Definition**: Sequential consistency guarantees that all operations appear in the same order to all clients, but not necessarily in real-time order.
- **Example**: Everyone agrees on the same sequence of writes, even if that order does not exactly match wall-clock timing.
- **Trade-offs**: It is weaker than linearizability and may allow behaviors that surprise users expecting real-time visibility.

### 11. Quorum
- **Definition**: A quorum is the minimum subset of replicas required to complete an operation while preserving a system's correctness model.
- **Example**: In a three-replica system, requiring two acknowledgments for a write is a majority quorum.
- **Trade-offs**: Quorums improve fault tolerance, but they can reduce availability when too many replicas are unreachable.

### 12. Durability
- **Definition**: Durability means that once a write is acknowledged, it will survive crashes according to the system's guarantees.
- **Example**: A committed transaction remains present after a database node restarts.
- **Trade-offs**: Stronger durability may require synchronous replication or write-ahead logging, which adds latency.

### 13. Fault Tolerance
- **Definition**: Fault tolerance is the ability to keep meeting key guarantees despite component failures.
- **Example**: A service continues operating after a node crash because traffic fails over to healthy replicas.
- **Trade-offs**: Higher fault tolerance increases operational complexity, cost, and coordination overhead.

### 14. Idempotency
- **Definition**: An operation is idempotent if applying it multiple times has the same effect as applying it once.
- **Example**: Retrying a payment request with the same idempotency key does not create a second charge.
- **Trade-offs**: Idempotency simplifies retries and recovery, but usually requires request identifiers, deduplication, or state tracking.

### 15. Delivery Semantics
- **Definition**: Messaging systems are often described by how many times a message may be delivered.
- **Common Models**:
  - **At-most-once**: A message is delivered zero or one time.
  - **At-least-once**: A message may be delivered more than once, so consumers must handle duplicates.
  - **Exactly-once**: Usually means the platform provides enough guarantees for the end result to occur once, but it is expensive and context-dependent.
- **Trade-offs**: At-least-once is common because it is practical; exactly-once semantics are narrower and harder than they sound.

### 16. Ordering Guarantees
- **Definition**: Ordering describes whether events are observed in a predictable sequence.
- **Example**: A per-partition log may preserve order for one key but not across the whole system.
- **Trade-offs**: Global ordering is expensive. Many systems provide partition-local ordering instead.

### 17. RPO and RTO
- **Definition**:
  - **RPO (Recovery Point Objective)**: The maximum acceptable amount of lost data after a failure.
  - **RTO (Recovery Time Objective)**: The maximum acceptable time to recover service.
- **Example**: An RPO of 5 minutes means losing up to 5 minutes of data is acceptable; an RTO of 30 minutes means service should be restored within 30 minutes.
- **Trade-offs**: Lower RPO and RTO targets require more redundancy, better automation, and higher cost.

### Summary

These concepts describe different dimensions of system behavior:

- **Safety vs liveness**: correctness versus eventual progress
- **Availability vs coordination**: responsiveness versus stricter agreement
- **Consistency models**: the exact rules for how reads observe writes
- **Operational resilience**: durability, fault tolerance, RPO, and RTO

When discussing system design, avoid saying a system is simply "consistent" or "available" without naming the exact guarantee, failure mode, and scope. Most real systems mix different guarantees for different operations.
