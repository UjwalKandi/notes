§4 — Spark (your highest-value prep; he will go here)

The mental model, in plain English

One driver (the manager) + many executors (the workers). The driver splits your job into tasks and hands them out. Data is split into partitions — one task per partition.

Lazy evaluation: transformations (filter, select, join) build a recipe and do nothing. An action (count, write, collect) triggers actual work. This is why "my Spark job did nothing for 10 minutes then exploded" — it was all queued up.

The one concept that explains 90% of Spark problems — the shuffle:

  • Narrow transformation = each partition works alone (filter, map). Cheap.
  • Wide transformation = data must move across the network to regroup (groupBy, join, distinct, repartition). This is a shuffle — writes to disk, sends over network, expensive.
  • Job → stages (split at every shuffle boundary) → tasks (one per partition).

  🗣️ "When I look at a slow Spark job, I look at stage boundaries first — every shuffle is a place where data hits disk and network, and that's usually where the time and the failures are."

Data skew — say this word, he'll respect it

If you group by customer_id and one customer has 40% of the rows, one task gets 40% of the data. 199 tasks finish in 30 seconds, one runs for 40 minutes and then OOMs. Classic, and it's the #1 real-world Spark failure.

Fixes: salting (append a random suffix to the hot key, aggregate twice), enabling AQE skew join handling, or broadcasting the small side of the join.

Broadcast join: if one table is small (a few hundred MB), ship a full copy to every executor and skip the shuffle entirely. Huge win on big-table-to-dimension joins.

🎤 The Spark-on-K8s OOM answer — rehearse this out loud twice

This is the single likeliest technical scenario, and it sits exactly at the intersection of his job and yours.

  "First I'd confirm which kind of kill, because there are three and they have different fixes. kubectl describe pod on an executor — if I see Last State: Terminated, Reason: OOMKilled, Exit Code 137, the container breached its memory limit and the kernel killed it. If it says Evicted, that's node-level pressure, not the pod's fault. And if the driver log shows a JVM OutOfMemoryError, that's inside the heap. Three different investigations.

  Then I'd scope it: driver or executors? All executors, or just one or two — because one or two dying points straight at skew. And I'd ask what changed: a code change, a config change, or a data-volume spike, which is usually the answer.

  In the Spark UI or history server I'd find which stage it dies in and compare max versus median task input size. Big gap means skew. High spill and shuffle-read means the partitions are too fat.

  The thing people miss is the memory math: the pod's ceiling has to cover spark.executor.memory plus memoryOverhead — off-heap, JVM native, and importantly PySpark worker processes live in that overhead. If the pod limit is under that sum, the kernel kills the container before Spark ever gets a chance to spill to disk. Python UDFs blow this up constantly.

  To restore service I'd bump memoryOverhead and the pod limit, drop executor.cores so fewer tasks share a heap, and raise spark.sql.shuffle.partitions so each task handles less data — then rerun.

  The real fix depends on the cause: salt the hot key or turn on AQE skew join for skew, switch a large-to-small join to broadcast, prune columns and push predicates down early, replace Python UDFs with native or pandas UDFs, and target roughly 128 to 200 MB per partition.

  Then prevention, which is the part I'd own — alert on executor OOM count and stage-level spill in Grafana, set ResourceQuotas per namespace so one job can't starve the cluster, and write the diagnostic sequence into a runbook. Plus a post-incident review with the team that owns the job."

That answer alone can carry the round.

Spark quick-recall

Term,One line
repartition vs coalesce,"repartition shuffles, can increase or decrease; coalesce merges without shuffle, decrease only"
cache/persist,Keep a reused DataFrame in memory — only worth it if you touch it 2+ times
Partition pruning,Query only the partitions you need — the reason you partition by date
Small-file problem,Thousands of tiny output files kill read performance → compact them
AQE (Adaptive Query Execution),"Spark 3+ re-optimizes at runtime: coalesces partitions, handles skewed joins"
Predicate/column pushdown,Filter and select at the file format layer (Parquet/Iceberg) before reading
Never do,collect() on a big DataFrame — pulls everything to the driver and kills it
