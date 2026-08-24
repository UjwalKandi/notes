## How to use this: every answer below is anchored to a bullet that's already on your resume

Your resume already claims Linux/Bash diagnostic scripts at **Epsilon** and **Apple**, and Docker at **Dover Fueling**. So Linux you speak about in **past tense, confidently** — it's on the page and it's defensible. Kubernetes you speak about at **workload level with a clear boundary** — because your resume lists it as a skill, not an accomplishment, and Matthew runs Spark in pods for a living.

The pattern for every answer: **which job → what the system was → what broke → what you ran → what you changed permanently.** That last part is what makes you sound like an SRE instead of someone who read a man page.

---

# 🐧 PART 1 — LINUX

## Your three story anchors (know which job each claim lives in)

| Job | The Linux story you own | Resume bullet backing it |
|---|---|---|
| **Epsilon** (Production Support Engineer) | Triage across 16 client environments over SSH; disk/inode failures killing batch jobs; Bash + cron automation; failover scripts | *"Used Linux command-line tools to inspect logs, trace running processes over SSH, isolate root causes"* · *"Bash and Python automation for data-quality checks, log rotation, pipeline health tests, scheduled via cron"* · *"Automated failover with Bash on Linux hosts, RTO 2h → 30m"* |
| **Apple** (current) | Bash diagnostic scripts inside RCA runbooks for recurring batch failures | *"Linux/Bash diagnostic scripts for recurring batch-job failures, −25% resolution time"* |
| **Dover Fueling** (Observability/SRE) | Resource-constrained Linux edge devices, containerized components | *"Edge diagnostics for resource-constrained IoT field devices"* |

---

## Answer 1 — "Tell me about your hands-on Linux experience"

> "Linux is where most of my production work happened. At Epsilon I was the Production Support Engineer covering 16 enterprise client environments — Disney, Bank of America, Unilever, Marriott — and when a pipeline broke, I was SSH'd into the host. RHEL and Ubuntu, mostly.
>
> Three kinds of work. First, **triage**: reading logs with `tail` and `grep`, tracing processes to find a runaway or hung ETL job, checking disk and memory. Second, **automation**: I wrote the Bash scripts for our scheduled data-quality checks, log rotation, and pipeline health tests, and scheduled them with cron so on-call wasn't doing manual checks every shift. Third, **recovery**: I automated production pipeline failover with Bash on the Linux hosts plus CloudFormation, which took our RTO from two hours down to thirty minutes.
>
> At Apple I've carried the same habits over — I standardized our RCA framework with reusable runbooks and Bash diagnostic scripts for the recurring batch-job failures, which cut resolution time about 25%.
>
> To be clear about the level: I'm an operator, not a sysadmin. I'm not tuning kernel parameters or compiling from source. I'm the person who gets paged, finds out which resource is the problem, restores service, and then writes the script and the runbook so it doesn't need a human next time."

**Why the last paragraph matters:** it defines your scope *before* he tests it. Now a hard kernel question isn't a gotcha — you already said that's outside your lane.

---

## Answer 2 — "Walk me through a production issue you diagnosed from Linux" ⭐

This is the highest-value question in the Linux section. It's real, it has commands, and the prevention half is what he'll respect as a data engineer.

> "The one I go back to is a nightly batch load at Epsilon that stalled. The client's data didn't land, and what made it nasty was that our alerting only fired on job *failure* — the process was still alive, so nothing paged. We found out from the client, which is the worst way to find out.
>
> I went to the host. `tail` on the job log showed it had stopped writing about 40 minutes earlier, no error. The process was still there but idle, and the last log line was a write. So I checked disk — `df -h` came back 100% on the staging volume.
>
> Then the part that actually took some experience: someone had already deleted old log files, but the space never came back. That happens when a process still has a file handle open — the kernel won't release the space until the process lets go. `lsof` shows you those deleted-but-open files. So I truncated the file in place instead of deleting, which frees the space immediately, restarted the job from the last checkpoint, and we landed inside the SLA window.
>
> Root cause was a log rotation config that rotated the file but never signaled the process to reopen it, so the same handle grew forever. We fixed the rotation.
>
> But the fix I cared more about was the detection gap. We added disk and inode alerts with thresholds set from actual growth rates rather than an arbitrary 90%, and — more importantly — we changed the alerting model from 'did the job exit non-zero' to 'is the job still making progress,' a heartbeat check. A stalled job and a failed job look completely different to a monitoring system, and only one of them was covered. That went into a runbook, and it was part of getting MTTR from 45 minutes to 12."

**Three things this answer does:** names a specific, common, non-obvious Linux behavior (deleted-open files); shows the stabilize-first / root-cause-second instinct; and lands on **a monitoring insight**, which is his four-year specialty. Say this one out loud twice tomorrow.

---

## Answer 3 — "How comfortable are you troubleshooting CPU, memory, disk, networking, and process issues?"

Don't just say "very comfortable." Show the **method**, then one concrete example per area.

> "Comfortable — and I work it in a fixed order so I'm not guessing under pressure.
>
> I start with the whole box: load average, and I read the 1, 5, and 15-minute numbers as a trend, because that tells me whether it's building or already recovering. Then I look at whether one process is responsible or whether everything is saturated — those are completely different investigations.
>
> **CPU** — find the process, then the thread. The thing I watch for is high load average with low CPU utilization, because that means the tasks are waiting on I/O, not compute, and adding CPU wouldn't help.
>
> **Memory** — I check `available`, not `free`, since Linux deliberately holds spare RAM as file cache and gives it back on demand. If a job disappeared with nothing useful in its log, I go straight to `dmesg`, because that usually means the OOM killer took it and the process never got a chance to write an error. That's a pattern I hit repeatedly with heavy ETL jobs.
>
> **Disk** — `df -h` for space, and `df -i` for inodes, which is the one people miss. A volume can be 30% full but out of inodes because something created millions of tiny files, and the application just sees 'no space left on device.' Then `du` to walk down and find what's eating it.
>
> **Network** — I break the request into phases before I let anyone blame the network. `curl` with a timing breakdown tells me whether the time went to DNS, TCP connect, TLS, or waiting on the first byte from the application. Most 'network problems' I've chased turned out to be a slow database or a saturated upstream.
>
> **Processes** — check the state. A process in uninterruptible sleep is blocked on I/O, and `kill -9` won't clear it, which surprises people. Zombies mean the parent isn't reaping children.
>
> And running through all of it: **what changed?** A deploy, a config edit, a data-volume spike, or something upstream. Systems that worked yesterday usually broke because something moved."

**That final paragraph is the most senior thing you can say in this entire interview.** It costs you nothing technically and it's exactly how experienced on-call engineers think.

---

## Answer 4 — "What automation have you built with Python or shell scripting?"

> "Three categories at Epsilon. **Scheduled validation** — Bash and Python data-quality checks running under cron on the Linux hosts, so instead of someone manually eyeballing row counts and file arrivals each shift, we got a report and an alert only when something was off. **Log rotation and housekeeping**, because disk-full was one of our top recurring failure causes and it's entirely preventable. And **failover and recovery** — scripted the recovery path with Bash plus CloudFormation, which is what took RTO from two hours to thirty minutes and held 99.9% uptime through the critical processing windows.
>
> At Sports Excitement I did the alerting side — Airflow trigger jobs wired into PagerDuty so failures pushed to on-call instead of waiting to be discovered, mean response under five minutes.
>
> My rule is pretty simple: anything I do twice at 3 a.m. becomes a script, and anything I script gets a runbook entry — because a script nobody knows about is worse than no script.
>
> One thing I'd flag about cron specifically: most of the cron failures I debugged weren't the script, they were the environment. Cron runs with a minimal PATH and none of your shell's variables, so something that works by hand fails silently at 2 a.m. Absolute paths everywhere, redirect output to a log, and lock the job so two runs can't overlap."

That last paragraph is a genuine practitioner detail. It's also cheap to remember.

---

## Linux follow-up survival kit

If he probes, these are the answers. Short is fine.

| His question | Your answer |
|---|---|
| "How do you find what's eating disk?" | `df -h` for the volume → `df -i` for inodes → `du -sh /var/* \| sort -h` and read the bottom, then walk down a level |
| "Disk shows full but you deleted the files" | Process still holds the file handle. `lsof` finds deleted-but-open files. `truncate -s 0` the file or restart the process. Real fix is log rotation. |
| "Job died, nothing in the log" | `dmesg -T \| grep -i killed` — OOM killer. It kills the process outright, so the app never writes anything. |
| "What's exit code 137?" | 128 + 9 = SIGKILL. Killed for memory — OOM killer on a host, OOMKilled in a container. 143 is 128 + 15, SIGTERM, a normal graceful shutdown. |
| "Load is 30 but CPU is 10% — what's wrong?" | Load counts tasks *waiting*, including on disk I/O. That's an I/O bottleneck; more CPU wouldn't help. |
| "Difference between `free` and `available`?" | `free` is truly untouched RAM. `available` includes reclaimable page cache. Look at `available` — low `free` alone is normal and healthy. |
| "How do you find the top errors in a large log?" | `grep ERROR app.log \| sort \| uniq -c \| sort -rn \| head` — group identical messages and rank by count. Tells you instantly if it's one bad upstream or systemic. |
| "How do you check if a service is running?" | `systemctl status <svc>` and `journalctl -u <svc> --since "15 min ago"` on systemd hosts; `ps aux \| grep` as a fallback |
| "What does a process in state D mean?" | Uninterruptible sleep — blocked in a kernel I/O call. Can't be killed with -9; you have to fix the underlying I/O. |
| "Permission denied on a write?" | Check `ls -l` and `id` for ownership and group, then walk the whole path since a parent directory can block you. Then umask and SELinux if it's RHEL. |
| Anything about kernel tuning, compiling, LVM, iptables internals | "That's outside what I've done — my Linux work is operational troubleshooting and automation rather than systems administration. Here's how I'd start figuring it out…" |

---

# ☸️ PART 2 — KUBERNETES

## The frame — read this before the answers

**Your resume has no Kubernetes bullet.** It's in the skills list. Matthew has been running Spark on K8s for years. So the play is:

- **Claim workload-level operations** — pods, jobs, logs, events, config, resource limits. This is defensible and it's *also his level*, since he submits Spark jobs, he doesn't administer clusters.
- **Anchor Docker in Dover Fueling**, which is on your resume.
- **State the boundary once, early, plainly.** Then stop apologizing. Say it once and move forward with confidence.
- **Mention the local cluster work.** If you get 20 minutes to `brew install kind` and run three commands tonight or tomorrow morning, do it — then "I've been running a local cluster to sharpen this" is literally true.

**What kills you:** "I manage our Kubernetes clusters." He'll ask about node upgrades, taints, or the CNI, and it's over. **What passes:** "I operate workloads on Kubernetes and I debug them the same way I debug anything in production — plus I know exactly where my depth ends."

---

## Answer 1 — "Describe your hands-on Kubernetes experience"

> "Let me be precise about the level, because I think that matters more than a broad claim.
>
> My container work started at Dover Fueling, where I containerized the diagnostic and metrics-exporter components with Docker as part of the observability stack I built. From there, my Kubernetes experience is **workload-level operations** rather than cluster administration. That means: deploying and supporting containerized services and jobs, reading pod status and events to figure out why something isn't healthy, pulling container logs — including from the previous instance when a pod has restarted, which is the part people miss — working with Deployments, Services, ConfigMaps, Secrets, namespaces, and Jobs, and dealing with resource requests and limits when something gets throttled or killed for memory. And on the monitoring side, getting pod and node metrics into Grafana dashboards, which is where I'm strongest overall.
>
> What I have **not** done: I haven't been the cluster admin. I haven't upgraded a control plane, managed the CNI, or written admission controllers. I don't want to oversell that.
>
> I've been running a local cluster to sharpen the deployment and troubleshooting side, because this is exactly where I want to grow, and honestly the debugging instincts transfer almost completely — a crashing pod is the same investigation as a crashing process, you're just reading events and container state instead of the process table."

That answer will land well with him. He'll trust everything else you say afterward.

---

## Answer 2 — "Walk me through what happens when you deploy into Kubernetes" ⭐

Pure knowledge question — no experience claim needed. Get this right and it does a lot of work for you.

> "You submit a manifest to the API server, which authenticates it, checks RBAC, validates it, and stores the desired state in etcd. Nothing has run yet — Kubernetes is declarative, so at this point you've just recorded what you want.
>
> Then the controllers take over. The Deployment controller creates a ReplicaSet, and the ReplicaSet creates Pod objects, which sit in Pending because they haven't been placed anywhere. The scheduler picks a node for each one by filtering on which nodes can actually fit the pod's resource requests, plus taints, tolerations, and affinity rules, then scoring the survivors.
>
> Once a pod is assigned, the kubelet on that node does the real work: pulls the image, gets an IP from the CNI plugin, mounts volumes and any ConfigMaps or Secrets, and starts the container. Then probes run — startup probe first if there is one, then readiness, and the pod only gets added to the Service's endpoints once it's *ready*. That's what keeps traffic off a container that's still warming up.
>
> For a rolling update, the Deployment brings up new pods and retires old ones gradually according to maxSurge and maxUnavailable, so there's always capacity serving. And because the old ReplicaSet is kept, a rollback is just scaling it back up.
>
> For a data workload it's usually a Job or CronJob rather than a Deployment — or in the Spark case, a driver pod that requests executor pods from the API server. Then what I care about shifts: resource requests, access to storage, and whether the job is idempotent on retry, because Kubernetes *will* restart things."

That last sentence ties Kubernetes to data-pipeline correctness. He'll feel it.

---

## Answer 3 — "A pod is repeatedly crashing. How do you troubleshoot it?" ⭐⭐

Most likely K8s question. Answer as a **sequence**, then **classify**.

> "Same discipline as any production issue — get the facts before touching anything.
>
> `kubectl get pods` first, to see the status and the restart count. Then `kubectl describe pod`, and I'm reading two things: the **Events** at the bottom, and **Last State**, which gives me the exit code and the reason the previous container died. That's usually where the answer is.
>
> Then `kubectl logs` — and critically `--previous`, because plain `logs` shows you the *new* container that just started, not the one that crashed. That trips people up constantly.
>
> Then `kubectl get events` sorted by timestamp for the namespace, in case the cause is outside the pod — node pressure, scheduling problems, a failing volume mount. And `kubectl top` for actual resource usage.
>
> From there it's classification. If Last State says **OOMKilled with exit code 137**, the container breached its memory limit — that's a resource problem, not application logic, so I stop reading the stack trace. If it's **CrashLoopBackOff** with an application error in the log, it's usually bad config, a missing environment variable, or a dependency that's down. **ImagePullBackOff** is a wrong tag or a missing pull secret. **CreateContainerConfigError** means a referenced ConfigMap or Secret key doesn't exist. If the pod is stuck in **Pending**, nothing has fit it — resource requests too large, a taint, or no available volume. And if the logs look fine but it keeps restarting anyway, I suspect the **liveness probe** is too aggressive — a probe with too short a timeout will kill a healthy but slow-starting container in a loop, which is a self-inflicted outage.
>
> One thing on ordering: if this is production and customers are affected, I restore service first — roll back to the last known-good revision — and do the root cause from the logs and events afterward. Debugging a live outage while it's still burning is the wrong trade."

That final paragraph is the SRE instinct. It's the difference between a candidate who knows commands and one who's actually been on call.

---

## Answer 4 — "Tell me about a real Kubernetes production problem"

Calibrated. Don't invent a cluster.

> "The pattern I've dealt with directly is containers being killed for memory. At Dover Fueling we ran containerized diagnostic and exporter components in a resource-constrained environment, so memory ceilings were a real operational concern, and the signature is always the same: the container restarts, and the application log tells you nothing useful because the process was killed outright and never got to write anything. The answer is in `describe` — `Last State: Terminated, Reason: OOMKilled, exit code 137` — and in `logs --previous` for whatever it managed to emit before it died.
>
> The thing I'd push back on is treating 'raise the limit' as the fix. You have to know which of three things it is: a limit that was genuinely set too tight, an actual leak where usage grows without bound, or a data-volume change where one partition or one batch got much larger than the others. The third is the most common one in data workloads and the one where raising the limit just buys you a week.
>
> And then the part I'd own: making it visible. Alerting on container restart counts and OOM kill events, not just on the job's final exit status — because a job that OOMs, retries, and eventually succeeds looks green on a dashboard while it's quietly degrading. That's the kind of gap I've spent most of my career closing."

**That closing point is genuinely strong** — it's a real observability insight, it's his specialty, and it's honest.

---

## Answer 5 — "Docker vs Kubernetes — what's each responsible for?"

> "Docker is build-and-run for containers on a single host — you write a Dockerfile, build an image in layers, push it to a registry, run it. It solves 'this runs the same everywhere.'
>
> Kubernetes is the orchestrator across many hosts. It decides *which* node runs your container, keeps the right number of replicas alive, restarts them when they die, gives them stable network identity through Services, handles rolling updates and rollbacks, injects config and secrets, and autoscales.
>
> So they're complementary, not competing: Docker produces the artifact, Kubernetes runs it at scale. I containerized components with Docker at Dover Fueling, and in an orchestrated environment Kubernetes is what schedules and heals them.
>
> Worth noting that Kubernetes doesn't actually use Docker as its runtime anymore — since 1.24 the kubelet talks to containerd through CRI. Docker-built images still work fine because they're OCI-standard, but 'Docker on Kubernetes' isn't literally accurate under the hood."

That last paragraph is a small, current, verifiable detail. It buys real credibility for one sentence of effort.

---

## Answer 6 — "What Kubernetes objects have you worked with?"

Answer in **tiers** — honest, and it shows you understand what each thing is for.

> "**Regularly:** Pods, Deployments, Services, ConfigMaps, Secrets, namespaces, and Jobs — that's the core of running and supporting a workload. Plus resource requests and limits, which in practice you can't avoid.
>
> **Understand and have worked around:** DaemonSets, since that's how monitoring and log-collection agents get onto every node, which is directly relevant to the observability work I've done. StatefulSets for anything needing stable identity and its own persistent volume, like Kafka or Cassandra. PVCs and StorageClasses. HPA for autoscaling.
>
> **Know conceptually but haven't owned:** RBAC roles and service accounts, network policies, PodDisruptionBudgets, ResourceQuotas per namespace, admission webhooks. I can explain what they're for and why you'd want them — PDBs so a node drain can't take out a quorum, quotas so one bad job can't starve a shared cluster — but I haven't been the person configuring them."

Three tiers is a much better answer than a flat list, because it tells him exactly where to pitch his follow-ups instead of guessing and catching you out.

---

## Kubernetes follow-up survival kit

| His question | Your answer |
|---|---|
| "Requests vs limits?" | Requests are what the **scheduler** uses to decide placement and are guaranteed. Limits are the enforced ceiling. Over the CPU limit you get **throttled**; over the memory limit you get **killed**. |
| "Difference between OOMKilled and Evicted?" | OOMKilled = this container exceeded **its own** memory limit; the kernel killed it. Evicted = the **node** ran out of memory or disk and kubelet evicted pods to save itself — that's not the pod's fault. Different fix. |
| "Liveness vs readiness?" | Liveness restarts a container that's hung. Readiness pulls it out of the Service's endpoints without restarting it. Startup probe protects a slow boot from a liveness kill. A too-aggressive liveness probe causes CrashLoops all by itself. |
| "How does a Service find its pods?" | Label selectors. The endpoints controller keeps a list of *ready* pod IPs, and kube-proxy programs the node's iptables/IPVS rules to route ClusterIP traffic to them. |
| "Deployment vs StatefulSet?" | Deployment = interchangeable stateless replicas, any order. StatefulSet = stable ordinal names, ordered startup, and a persistent volume per pod — for Kafka, Cassandra, databases. |
| "How do you roll back?" | `kubectl rollout undo deployment/<name>` — the old ReplicaSet is retained, so it scales back up. `rollout history` shows revisions. In an incident, that's my first move: restore, then investigate. |
| "ConfigMap vs Secret?" | Both inject config as env vars or mounted files. Secrets are base64 in etcd and should be encrypted at rest with RBAC restricting access — base64 is encoding, not encryption, so Secrets alone aren't real protection. |
| "How would you monitor a cluster?" | Prometheus scraping kube-state-metrics, node-exporter, and cAdvisor, with Grafana on top. Alert on pod restart counts, OOMKill events, pods stuck Pending, node conditions like MemoryPressure and DiskPressure, and job failure rates. Symptom-based, not raw CPU. |
| "How do you get into a running pod?" | `kubectl exec -it <pod> -- sh`. And `kubectl port-forward` to reach a service locally without exposing it. |
| Node upgrades, CNI internals, etcd, cluster autoscaler internals, operators | "That's cluster administration, which I haven't owned — my experience is workload-level. Conceptually I know that [one honest sentence], but I'd be learning the operational specifics." |

---

## 🌉 The two bridge answers that carry the most weight

These aren't Linux or Kubernetes questions — they're the ones where your real experience is strongest, and they're likely to come up.

### "How have you monitored production infrastructure or data platforms?"

> "I've built the stack three times now, so I've got a fairly settled view.
>
> At Epsilon it was CloudWatch, Dynatrace, and Kibana — metrics, distributed tracing, and log analysis — and the win there was establishing baselines and anomaly thresholds, which caught about 60% of issues before they reached the customer. At Dover Fueling I built a three-pillar stack from scratch: Grafana dashboards, Azure APIM log analytics, and Kafka metrics exporters. Detection went from 15 minutes to 3, MTTR down 40%. At Apple it's Grafana dashboards and alerting on pipeline health, which took mean time to detection from hours to under 15 minutes.
>
> On infrastructure I care about the golden signals — latency, traffic, errors, saturation. But the thing I'd emphasize for a data platform is that host-level monitoring isn't enough. A pipeline can exit zero and still have written garbage. So I also monitor the **data**: freshness, row-count deltas against baseline, null rates, schema drift, duplicate rate, job duration versus normal, and consumer lag on streaming. Those are the signals that actually protect the analytics and ML consumers downstream.
>
> And the discipline that matters most is alerting on symptoms with customer impact rather than every raw metric. At Dover Fueling I retuned thresholds and escalation and cut alert fatigue 45% while keeping 100% detection on P1s and P2s — because an on-call rotation that has learned to ignore alerts is worse than having no alerts at all."

That paragraph about monitoring the data is the closest thing you have to a bullseye. **Matthew spent four years building metrics with thresholds to alert stakeholders about data issues.** Deliver it well.

### "Describe a major production incident — identify, restore, root cause, prevent"

Use the disk-full story from Answer 2, structured explicitly in his four parts. Then add the general frame:

> "That's the shape of every incident I've run: stabilize first, root-cause second, prevent third, and document fourth. At Epsilon that discipline took MTTR from 45 minutes to 12 across 16 client environments with 99.8% SLA compliance, and I authored 20+ runbooks and postmortems in a blameless format — which cut repeat incident categories by 50%. That last number is the one I'm proudest of, because it means the fixes were actually structural, not just faster firefighting."

---

## ⛔ The danger list — do not say these

| Don't say | Say instead |
|---|---|
| "I manage our Kubernetes clusters" | "I operate workloads on Kubernetes" |
| "I've deployed and configured Kubernetes" | "I've deployed *into* Kubernetes" |
| "I'm an expert in Linux" | "Linux is where most of my production troubleshooting happened" |
| "I set up our Terraform infrastructure" | "I've worked with Terraform for provisioning; I'm strongest reading and extending existing modules" |
| "Yes, definitely" (to a tool you barely know) | "Limited hands-on there — closest thing I've done is X" |
| Silence when stuck | "Let me think about that out loud for a second…" |

**The universal escape hatch, memorize it word for word:**

> "I haven't done that hands-on. The closest thing I've worked on is ___, and here's how I'd approach ___ based on that — I'd want to verify ___ first."

Then actually reason through it. This is a *good* answer, not a concession. He's a practitioner; he can tell the difference between someone learning and someone bluffing, and only one of those is disqualifying.

---

## ⏱ Tomorrow morning: 1h45m for both

| Time | Do this |
|---|---|
| **8:00–8:20** | Read Linux Answers 1–4 once. Don't memorize — get the shape. |
| **8:20–8:45** | Say **Answer 2** (the disk-full incident) **out loud, twice, standing up.** Time it — aim 90 seconds. |
| **8:45–9:00** | Skim the Linux follow-up table. Cover the right column, test yourself on the 6 bolded ones. |
| **9:00–9:20** | Read K8s Answers 1–3. |
| **9:20–9:40** | Say **Answer 1** (the honest frame) and **Answer 3** (crashing pod) **out loud, twice each.** |
| **9:40–9:45** | Skim the K8s follow-up table. Know: requests vs limits, OOMKilled vs Evicted, liveness vs readiness, `logs --previous`. |
| **Optional, if you have 20 min** | `brew install colima kind kubectl && colima start && kind create cluster && kubectl get nodes && kubectl create deployment demo --image=nginx && kubectl get pods && kubectl describe pod <name>` — five commands, and "I've been running a local cluster" becomes literally true. |

**The two answers that matter most:** the **disk-full incident** (Linux Answer 2) and the **honest Kubernetes frame** (K8s Answer 1). If you only rehearse two things out loud, rehearse those. One proves you've actually been in production; the other buys you trust for the whole rest of the conversation.
