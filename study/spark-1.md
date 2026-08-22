I'll pin down exactly how much prep time you have, then give you the recalibrated plan — because this interviewer changes the priorities significantly.

## ⏱️ 4 days, 1 hour out — and the calculus just changed

**Tuesday, Aug 25, 12:00 PM CDT.** ~25 realistic study hours. But the bigger news is *who* you're talking to.

---

# 🎯 Read on Matthew Cohen (this is the most important section)

He is **not an SRE**. He's a **Spark/Airflow/Scala data engineer** who spent four years as a **Data Quality Engineer** building metrics with thresholds to alert stakeholders on data issues. Look at his actual history:

| What his profile says | What it means for your interview |
|---|---|
| "Sr. Data Engineer \| **Spark, Kubernetes, Airflow**" — his own headline | These three are what he'll go deep on. Spark is his home turf. |
| Migrated **Pig → Spark**, added K8s + Airflow | He'll ask Spark like a practitioner, not from a checklist |
| Spark on **Kubernetes pods via managed Airflow**, "packaged JAR portable to any orchestrator" | 🔥 His K8s experience is **workload-level, exactly like yours** — he runs Spark in pods; he's not a cluster admin either |
| "Created **metrics with thresholds to alert** stakeholders, product teams, and clients when there is a data issue" + **150+ metrics** to Tableau | 🔥🔥 **This is literally your entire career.** Biggest connection point in the whole interview |
| Reduced DAG completion 1hr → 20min; linear → parallel Airflow, **60% runtime cut**; cheaper backfills; X execution dates in parallel | He is obsessed with **pipeline performance and parallelism**. Have opinions here. |
| Apache **Iceberg** tables → API → BI dashboards | Iceberg will likely come up. Know the 4 basics. |
| **Scala**, TDD, unit testing across Dev/QA/Prod, Objects/Classes, code reusability | He values **testing discipline** — your pre-deployment validation framework is the match |
| Led 3 engineers from another consulting firm; **delivered best-practice presentations** | Values knowledge transfer → your 20+ runbooks + Seeed workshops |
| **No Terraform anywhere.** No Splunk beyond a "Learning Splunk" cert. No Linux-admin history. | ⬇️ Terraform and deep Linux internals are **low-probability**. Massive relief. |
| Runs **two side businesses** (Shopify e-comm, sock brand), was a startup CIO | Entrepreneurial. He will genuinely light up at your **hackathon wins** and startup work. Show that energy. |
| SUNY Oswego, Rockville Centre NY, remote | Peer-level, not an Apple gatekeeper. Round 1 is a *"can I work with this guy"* screen. |

### The three strategic conclusions

1. **Reweight your prep hard toward Spark + Airflow + data quality.** The recruiter's generic question list overweights Linux/Terraform. The actual human asking has a Spark/Airflow/DQ brain.
2. **Your K8s "weakness" mostly evaporates.** He runs Spark in pods through Airflow. When you say *"I've operated at the workload level — pods, jobs, logs, OOMKilled, ConfigMaps — not as cluster admin,"* he will nod, because **that's his level too**. Don't over-apologize.
3. **You two are mirror images.** He built metric thresholds to catch data issues; you built dashboards and alerting to catch data issues. Frame yourself as *the operations half of what he does*. That's the whole pitch.

---

# 🔁 The mirror map — memorize these four pairings

Weave these in naturally. Not "I saw on your LinkedIn," but as your own framing.

| He did | You did | Say it like this |
|---|---|---|
| Metrics + thresholds alerting stakeholders to data issues; 150+ metrics in Tableau | Grafana dashboards + alerting, MTTD hours → **<15 min**; alert tuning cut fatigue **45%** with 100% P1/P2 coverage | *"The part I care most about is alerting on the data itself — freshness, volume, null rates, schema drift — not just host health. Job-status alerts miss silent corruption."* |
| Linear → parallel Airflow, 60% runtime cut, cheap backfills | 50+ daily DAGs, retry logic, SLA-breach alerts, automated recovery, **99.8%** reliability | *"I've owned 50+ daily DAGs — my focus was reliability engineering on them: retries, SLA breach alerts, idempotent reruns so backfills are safe."* |
| Unit testing across Dev/QA/Prod, TDD | Pre-deployment data-quality validation + load testing → **28%** fewer post-release failures | *"We treated data like code — validation gates before deploy, which cut post-release pipeline failures 28%."* |
| Led 3 engineers, taught best-practice sessions | 20+ runbooks/SOPs, blameless RCA, **50%** fewer repeat incidents, 10+ workshops to 100+ students | *"I write things down obsessively — runbooks are how a team stops solving the same incident twice."* |

---

# 📅 Recalibrated 4-day roadmap (25 hours)

| Block | When | Hrs | Focus | Change vs. before |
|---|---|---|---|---|
| 🔴 1 | Fri eve | 3 | **Spark deep dive** (§4) — architecture, shuffle, skew, OOM scenario | ⬆️ **Promoted to #1** |
| 🔴 2 | Sat AM | 3 | **Airflow deep dive** (§5) + data quality framing | ⬆️ **Promoted** |
| 🔴 3 | Sat PM | 3 | **Hands-on lab**: Docker → kind → break pods → PySpark local | Same, still essential |
| 🟠 4 | Sat eve | 1 | Iceberg + Scala minimum (§6) | ⬆️ New |
| 🔴 5 | Sun AM | 3 | SQL — type all 12 queries cold | Same |
| 🟠 6 | Sun midday | 2 | Python patterns, out loud with complexity | Same |
| 🟠 7 | Sun PM | 2 | Linux: the 8 commands + 4 senior facts (from my last message) | ⬇️ **Demoted** — still a gate, but he's not a Linux guy |
| 🟢 8 | Mon AM | 2 | K8s: deploy flow + CrashLoop/OOM triage | Slightly ⬇️ |
| 🟠 9 | Mon midday | 1 | Terraform 20-minute version only (§7) | ⬇️⬇️ **Heavily demoted** |
| 🔴 10 | Mon PM | 3 | **Behavioral out loud, timed** (§8) + your questions for him | Same |
| 🟢 11 | Tue AM | 2 | Cheat sheet, Teams test, warm up voice | Same |

---

# 🔥 §4 — Spark (your highest-value prep; he will go here)

### The mental model, in plain English

One **driver** (the manager) + many **executors** (the workers). The driver splits your job into tasks and hands them out. Data is split into **partitions** — one task per partition.

**Lazy evaluation:** transformations (`filter`, `select`, `join`) build a recipe and do nothing. An **action** (`count`, `write`, `collect`) triggers actual work. This is why "my Spark job did nothing for 10 minutes then exploded" — it was all queued up.

**The one concept that explains 90% of Spark problems — the shuffle:**
- **Narrow** transformation = each partition works alone (`filter`, `map`). Cheap.
- **Wide** transformation = data must move across the network to regroup (`groupBy`, `join`, `distinct`, `repartition`). **This is a shuffle** — writes to disk, sends over network, expensive.
- Job → **stages** (split at every shuffle boundary) → **tasks** (one per partition).

> 🗣️ *"When I look at a slow Spark job, I look at stage boundaries first — every shuffle is a place where data hits disk and network, and that's usually where the time and the failures are."*

### Data skew — say this word, he'll respect it

If you group by `customer_id` and one customer has 40% of the rows, **one task gets 40% of the data**. 199 tasks finish in 30 seconds, one runs for 40 minutes and then OOMs. Classic, and it's the #1 real-world Spark failure.

**Fixes:** salting (append a random suffix to the hot key, aggregate twice), enabling **AQE skew join** handling, or broadcasting the small side of the join.

**Broadcast join:** if one table is small (a few hundred MB), ship a full copy to every executor and skip the shuffle entirely. Huge win on big-table-to-dimension joins.

### 🎤 The Spark-on-K8s OOM answer — rehearse this out loud twice

This is the single likeliest technical scenario, and it sits exactly at the intersection of his job and yours.

> "First I'd confirm **which** kind of kill, because there are three and they have different fixes. `kubectl describe pod` on an executor — if I see `Last State: Terminated, Reason: OOMKilled, Exit Code 137`, the container breached its memory limit and the kernel killed it. If it says `Evicted`, that's node-level pressure, not the pod's fault. And if the driver log shows a JVM `OutOfMemoryError`, that's inside the heap. Three different investigations.
>
> Then I'd scope it: driver or executors? All executors, or just one or two — because one or two dying points straight at **skew**. And I'd ask what changed: a code change, a config change, or a data-volume spike, which is usually the answer.
>
> In the Spark UI or history server I'd find which stage it dies in and compare max versus median task input size. Big gap means skew. High spill and shuffle-read means the partitions are too fat.
>
> The thing people miss is the memory math: the pod's ceiling has to cover `spark.executor.memory` **plus** `memoryOverhead` — off-heap, JVM native, and importantly PySpark worker processes live in that overhead. If the pod limit is under that sum, the kernel kills the container before Spark ever gets a chance to spill to disk. Python UDFs blow this up constantly.
>
> To restore service I'd bump `memoryOverhead` and the pod limit, drop `executor.cores` so fewer tasks share a heap, and raise `spark.sql.shuffle.partitions` so each task handles less data — then rerun.
>
> The real fix depends on the cause: salt the hot key or turn on AQE skew join for skew, switch a large-to-small join to broadcast, prune columns and push predicates down early, replace Python UDFs with native or pandas UDFs, and target roughly 128 to 200 MB per partition.
>
> Then prevention, which is the part I'd own — alert on executor OOM count and stage-level spill in Grafana, set ResourceQuotas per namespace so one job can't starve the cluster, and write the diagnostic sequence into a runbook. Plus a post-incident review with the team that owns the job."

That answer alone can carry the round.

### Spark quick-recall
| Term | One line |
|---|---|
| `repartition` vs `coalesce` | `repartition` shuffles, can increase or decrease; `coalesce` merges without shuffle, decrease only |
| `cache`/`persist` | Keep a reused DataFrame in memory — only worth it if you touch it 2+ times |
| Partition pruning | Query only the partitions you need — the reason you partition by date |
| Small-file problem | Thousands of tiny output files kill read performance → compact them |
| AQE (Adaptive Query Execution) | Spark 3+ re-optimizes at runtime: coalesces partitions, handles skewed joins |
| Predicate/column pushdown | Filter and select at the file format layer (Parquet/Iceberg) before reading |
| Never do | `collect()` on a big DataFrame — pulls everything to the driver and kills it |

---

# 🌀 §5 — Airflow (he'll test whether you *own* it or just click it)

**Architecture:** scheduler (decides what runs) + executor (Celery/Kubernetes) + webserver + metadata DB. A **DAG** is the dependency graph; each **task** is an operator; a **sensor** waits for something.

**Reliability knobs — these are yours, own them:**
- `retries` + `retry_exponential_backoff` — your DAG-level retry logic
- `sla` → SLA-miss callback → **your PagerDuty integration, sub-5-min response**
- `on_failure_callback` for alerting/auto-recovery
- `pools` and `max_active_runs`/`concurrency` to stop one DAG starving the scheduler
- `catchup=False` unless you actually want a backfill storm on deploy
- `depends_on_past` for sequential dependencies

**⭐ Idempotency — the word that marks you as senior.** A task must produce the identical result if rerun. Practically: **delete-and-insert the partition** rather than appending, key writes on a natural key, use the run's `data_interval` instead of `datetime.now()`. Without it, retries silently double your data.

> 🗣️ *"Every task I write is idempotent and partition-scoped — overwrite the partition, never blind-append — because retries and backfills are guaranteed to happen, and non-idempotent tasks turn a retry into a data-quality incident."*

**Parallelism (he cut runtime 60% doing this — be conversant):** wide DAGs over long chains, `max_active_tasks`, dynamic task mapping (`.expand()`) to fan out per-partition, `KubernetesPodOperator` for isolated resources per task, deferrable sensors so waiting tasks don't hold worker slots.

**Failures you should be able to name instantly:**

| Symptom | Cause |
|---|---|
| Task stuck in `queued` forever | Pool exhausted / no workers / concurrency cap |
| **Zombie task** | Worker died mid-run; scheduler reaps it — retries must be safe |
| Scheduler lag, DAGs late | Too many DAG files, slow parsing, heavy top-level code |
| Sensors eating all slots | Use `mode="reschedule"` or deferrable operators |
| Wrong date processed | Confused `execution_date`/`data_interval` vs `now()` |
| Backfill destroyed prod | `catchup=True` on deploy |

---

# 🧊 §6 — Iceberg + Scala (30 minutes total, high leverage)

**Apache Iceberg** (he moves Iceberg data to BI dashboards — 4 facts is plenty):
1. **Open table format** on object storage — brings database behavior to files in S3.
2. **Snapshots + time travel** → ACID commits, and you can query "as of" a prior state or roll back a bad write. *Ops gold: instant rollback for a corrupted load.*
3. **Schema and partition evolution** — add/rename columns or change partitioning without rewriting the table. **Hidden partitioning** means queries don't need to know the layout.
4. **Ops chores** — compact small files, expire old snapshots, clean orphan files. Skipping these is why Iceberg tables get slow.

> 🗣️ *"What I like about Iceberg operationally is snapshot rollback — when a bad upstream feed lands, you revert to the previous snapshot instead of running a recovery job. That's a real MTTR reducer."*

**Scala** (dropped from the coding list, but it's *his* language — have one honest sentence):
> "My production languages are Python, Bash, and SQL. I've read Scala Spark code and I understand the DataFrame API is nearly identical across both, plus the JVM/JAR packaging and typing differences. If the team writes Scala Spark, I'd expect a few weeks to be productive, and I'd lean on unit tests to get there safely."

Do **not** claim Scala fluency. He's written it for years and will know in one question.

---

# 🧱 §7 — Terraform: the 20-minute version

He has zero Terraform on his profile. Know the **shape**, don't burn hours:

`init` → `validate` → `plan -out` → `apply` → `destroy`. State file maps your config to real resource IDs; **remote state in S3 + DynamoDB for locking**; `plan` is how you detect **drift** when someone changes something in the console; `for_each` over `count` so removing one item doesn't re-index and destroy others; never commit state (it contains secrets).

Your honest answer from the earlier prep still stands — deliver it in **60 seconds**, then pivot: *"...and where I've had the most impact is the reliability side — the observability and incident work around the infrastructure."*

---

# 🎤 §8 — Behavioral answers, retuned for Matthew

Keep every one to **90 seconds.** Only 27 minutes of substance exist.

### "Walk me through your resume" (your opener — this is scored highest)
> "Sure — the short version is I've spent four-plus years on the operations side of data platforms, and I've gotten progressively closer to the pipelines themselves.
>
> I started at Epsilon in Bangalore as a Data Programmer, then got promoted to Production Support Engineer, running incident management across 16 enterprise client environments — Disney, Bank of America, Unilever, Marriott. That's where I learned incident response for real: MTTR from 45 minutes down to 12, 99.8% SLA compliance, and I built the observability stack on CloudWatch, Dynatrace, and Kibana that caught 60% of issues before customers saw them.
>
> Then I moved to Austin for my Master's at UT Austin, and did two roles that pushed me deeper into the platform. At Dover Fueling I built a three-pillar observability stack — Grafana, APIM log analytics, Kafka exporters — and cut detection from 15 minutes to 3. At Sports Excitement I owned 50+ daily Airflow ETL pipelines end to end: retry logic, SLA-breach alerting, PagerDuty integration, 99.8% job reliability, plus ADLS partition tuning that took query latency from 1.2 seconds to 550 milliseconds.
>
> Now I'm at Apple on the metadata side, running ingestion and quality validation pipelines for the Apple TV global content catalog across 100+ international partner feeds. Grafana dashboards and alerting there took MTTD from hours to under 15 minutes, and the RCA framework and Bash diagnostic runbooks I standardized cut resolution time 25%.
>
> The thread through all of it is data reliability — making sure pipelines don't just run, but produce correct data on time, and that when they break, someone knows in minutes and there's a runbook. That's why this role caught my attention."

### "A production incident you diagnosed from Linux"
Use the disk-full / deleted-open-files story from my earlier message — it's concrete, it has real commands, and the prevention half (stall detection on heartbeat, not exit code) is what he'll actually value as a data engineer.

### "Tell me about a real Kubernetes production problem"
Calibrated version — don't invent a cluster you never ran:
> "The one I've dealt with directly is containers being killed for memory. At Dover Fueling we ran containerized diagnostic and metrics-exporter components, and I've debugged the pattern in orchestrated environments: the pod restarts, the app log shows nothing useful because it never got to write, and the answer is in `kubectl describe` — `Last State: Terminated, Reason: OOMKilled, exit code 137`. The trap is that `kubectl logs` shows you the *new* container; you need `--previous` to see the one that died.
>
> The fix is rarely just 'raise the limit.' You have to know whether it's a genuine limit that's too tight, a leak, or a data-volume spike hitting one partition harder than the others — and the third is the most common in data workloads.
>
> I want to be straight with you: I've operated at the workload level — pods, deployments, jobs, services, ConfigMaps, secrets, namespaces, reading events and logs, hooking pod and node metrics into Grafana. I haven't been the cluster admin upgrading control planes. I've been running a kind cluster locally to sharpen the deployment and troubleshooting side, because this is where I want to grow, and my incident instincts transfer directly."

### "Identified a problem yourself and took ownership"
Your Apple RCA-framework story from the earlier prep — nobody asked, you noticed the repeated debugging, you built the runbooks and dashboards, −25% resolution / −30% feed failures / MTTD hours → 15 min. **Add one line he'll feel:** *"And those feeds are upstream of search and recommendations, so a silent data-quality issue becomes a bad customer experience — that's what made it worth owning."*

### "Why would the team be better with you on it?" (your close)
> "Three things. One — I've run incidents under SLA pressure for Fortune 500 clients, so I stabilize first and root-cause second, and I always close the loop with prevention rather than just closing the ticket. Two — I build **data** observability, not just infrastructure monitoring: freshness, volume deltas, schema drift, null rates, consumer lag. Host CPU dashboards don't catch silently wrong data, and wrong data is what actually hurts the analytics and ML consumers downstream. Three — I document and automate compulsively. Runbooks, RCAs, self-service tooling. That's what makes on-call survivable and lets a team move fast with limited direction."

### Energy note (the recruiter flagged culture fit twice)
He runs two side businesses and was a startup CIO. **Bring your hackathon wins up** — first place at ElevenLabs against 1,000+ people, DFS internal hackathon win presented to executives, Seeed workshops. Say *"I build things on weekends because I like it."* That's his language.

---

# ❓ §9 — Questions for him (ask 3, one must be technical)

Specificity here is your best signal. These are drawn from what he actually does:

1. **"Is Spark running on Kubernetes for this team, or on a managed platform like Databricks or EMR — and is orchestration Airflow?"** (Sets you up to talk his stack.)
2. **"How do you handle data-quality monitoring today — is it metrics-with-thresholds on the datasets themselves, or mostly job-level success/failure alerting?"** 🔥 This is *his* four-year specialty. He will talk for three minutes and rate you highly for asking.
3. **"Where's the biggest operational pain right now — pipeline reliability, deployment velocity, or cost and cluster efficiency?"**
4. **"What does on-call look like, and what's the most common source of pages?"**
5. **"What do the people who succeed on this team have in common?"** (Recruiter explicitly suggested this.)
6. 🔒 **Always close with: "Is there anything in my background you'd like me to go deeper on before we wrap?"** — your safety net to repair a weak answer.

---

# 🗓️ §10 — Interview-day mechanics

**Only 27 minutes of substance.** Discipline matters more than knowledge.

| Item | Do this |
|---|---|
| Teams link | Test it **Monday**, not Tuesday. Join 5 min early — Riley or Alena will be on to introduce. |
| Setup | Wired headphones, camera at eye level, light in front of you, phone silenced, backup hotspot ready |
| On screen | Your resume + a **one-page** cheat sheet only. Not this document. |
| Answer length | 60–90 seconds, then **stop and ask** "want me to go deeper there?" Rambling is the #1 failure mode in a 30-min screen. |
| If coding appears | Restate the problem → name edge cases → say the brute force + complexity → write the good version → walk one example. **Narrate constantly.** Silence reads as stuck. |
| When you don't know | *"I haven't done that hands-on. Closest thing I've done is X — here's how I'd approach it."* Then actually reason. **Never bluff.** He's a practitioner; he'll catch it instantly and it's the one unrecoverable mistake. |
| Close | "I'm genuinely excited about this — data reliability and observability is exactly the work I want to be doing, and it sounds like there's real scope here. What are the next steps?" |
| After | Reply to Riley same day confirming; thank-you note within a few hours. |

---

# 🃏 §11 — One-page cheat sheet (this is all you look at Tuesday)

**Spark:** driver + executors; lazy until action; stages split at shuffles; narrow vs wide; **skew** = one fat partition → salt / AQE / broadcast; pod limit must cover `executor.memory` + `memoryOverhead` (PySpark lives in overhead); **137 = OOMKilled**, `Evicted` = node pressure, JVM OOM = heap; never `collect()`.
**Airflow:** scheduler/executor/webserver/metadata DB; retries + backoff; SLA callback → PagerDuty; pools; `catchup=False`; **idempotent = overwrite the partition, use `data_interval` not `now()`**; zombie task = dead worker.
**Iceberg:** snapshots + time travel + rollback, ACID, schema/partition evolution, hidden partitioning; compact + expire snapshots.
**K8s debug:** `get pods` → `describe` (Events + Last State) → **`logs --previous`** → `get events` → `top` → `rollout undo`. Requests = scheduling, limits = enforcement. CPU over = throttled, memory over = killed.
**Linux:** `uptime` (trend) → `top` → `free -h` (**`available`**, not free) → `df -h` then **`df -i`** → `dmesg -T \| grep -i killed` → `grep ERROR log \| sort \| uniq -c \| sort -rn`. High load + low CPU = I/O wait. Deleted-but-open files don't free space.
**SQL:** `WHERE` before groups, `HAVING` after; `ROW_NUMBER() OVER (PARTITION BY key ORDER BY updated_at DESC) = 1` to dedupe; `LEFT JOIN ... IS NULL` = anti-join; `COALESCE`; never wrap an indexed date column in a function.
**Terraform:** init → plan → apply; remote state S3 + DynamoDB lock; plan detects drift; `for_each` > `count`.
**Your numbers:** MTTR 45→12 min (−75%) · 99.8% SLA · 16 client environments · 100+ partner feeds · MTTD hours→<15 min · 50+ DAGs @ 99.8% · detection 15→3 min · alert fatigue −45% · RTO 2h→30m · latency 1.2s→550ms · −28% post-release failures · 20+ runbooks.

---

## The three things that decide Tuesday

1. **Do the Spark work Friday night.** He's a Spark engineer. If your Spark answer is crisp and your Terraform answer is honest-but-thin, you pass. Reverse that and you don't.
2. **Do the Saturday lab.** Three hours of `kind` + a real OOMKilled pod + a local PySpark job turns "I've read about this" into "I did this last weekend." He'll hear the difference in your voice.
3. **Lead with data reliability, not infrastructure.** He spent four years alerting on data issues. Your identity in that room is *"the operations and observability half of the data platform"* — not *"an aspiring Kubernetes admin."* Play the position where you're genuinely strong and he's genuinely interested.
