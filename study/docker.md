# 🐳 Tonight's Lab: Docker → your own image → a real Kubernetes cluster

**3 hours. Everything is copy-paste. Type the short ones by hand — that's how it sticks.**

Ground rule: **when something breaks, don't skip it — that's the lab.** Debugging a broken container *is* the skill Matthew told you to get. Jump to the troubleshooting table at the bottom if you get stuck for more than 5 minutes.

---

## Block 0 (20 min) — Install and verify

You need a container runtime. **Colima** is lightweight and free (Docker Desktop needs a license at most companies).

```bash
brew install colima docker kubectl kind
colima start --cpu 4 --memory 8 --disk 40
```

That takes 2–3 minutes. Then verify:

```bash
docker version          # should print Client AND Server sections
docker run hello-world  # should print "Hello from Docker!"
kind version
kubectl version --client
```

✅ **Checkpoint:** `docker run hello-world` prints the hello message. If the Server section is missing, `colima status` then `colima restart`.

> 🧠 **What you just did:** Colima runs a tiny Linux VM on your Mac (containers are a *Linux* feature — they can't run natively on macOS). `docker` is the client talking to the daemon inside that VM.

---

## Block 1 (30 min) — Docker fundamentals

Type these one at a time and **read the output.**

```bash
# 1. Run a container and get a shell inside it
docker run -it --rm ubuntu bash
```
You're now *inside* a container. Try it:
```bash
cat /etc/os-release      # Ubuntu — even though you're on a Mac
ls /
ps aux                   # almost nothing running — containers are isolated
hostname
exit                     # container is destroyed (--rm)
```

```bash
# 2. Run something in the background and inspect it
docker run -d --name web -p 8000:80 nginx
docker ps                        # RUNNING containers
docker ps -a                     # including stopped ones
curl localhost:8000              # you should get nginx's HTML
docker logs web                  # nginx access logs
docker exec -it web bash         # get a shell in a RUNNING container
  ls /usr/share/nginx/html
  exit
docker stats --no-stream web     # live CPU/memory
docker inspect web | head -40    # full config as JSON
```

```bash
# 3. Clean up
docker stop web
docker rm web
docker images                    # local image cache
```

**The 4 flags you must know cold:**

| Flag | Meaning |
|---|---|
| `-d` | detached — run in background |
| `-it` | interactive + terminal — for shells |
| `--rm` | delete the container when it exits |
| `-p 8000:80` | **host port : container port** |

> 🧠 **Image vs container:** an **image** is the frozen blueprint (like a class). A **container** is a running instance of it (like an object). One image → many containers.

🗣️ **Interview line:** *"An image is the immutable artifact; a container is a running instance of it. Same image runs identically on my laptop and in the cluster — that's the whole point."*

---

## Block 2 (40 min) — Build your own image ⭐

This is the block that matters most. Create a working directory:

```bash
mkdir -p ~/k8slab && cd ~/k8slab
```

### The app (stdlib only — no dependencies to install)

```bash
cat > app.py <<'EOF'
import os, json
from http.server import BaseHTTPRequestHandler, HTTPServer

VERSION  = os.environ.get("APP_VERSION", "v1")
GREETING = os.environ.get("GREETING", "hello")

class Handler(BaseHTTPRequestHandler):
    def do_GET(self):
        if self.path == "/healthz":
            body = {"status": "ok"}
        else:
            body = {"greeting": GREETING, "version": VERSION, "pod": os.uname().nodename}
        payload = json.dumps(body).encode()
        self.send_response(200)
        self.send_header("Content-Type", "application/json")
        self.send_header("Content-Length", str(len(payload)))
        self.end_headers()
        self.wfile.write(payload)

    def log_message(self, fmt, *args):
        print("%s %s" % (self.address_string(), fmt % args), flush=True)

if __name__ == "__main__":
    print("starting demo %s on port 8080" % VERSION, flush=True)
    HTTPServer(("0.0.0.0", 8080), Handler).serve_forever()
EOF
```

### The Dockerfile — type this one by hand

```bash
cat > Dockerfile <<'EOF'
FROM python:3.12-slim
WORKDIR /app
COPY app.py .
ENV APP_VERSION=v1
EXPOSE 8080
CMD ["python", "-u", "app.py"]
EOF
```

**Every line explained — know all six:**

| Line | What it does |
|---|---|
| `FROM python:3.12-slim` | Base image. `-slim` = smaller = faster pulls, smaller attack surface |
| `WORKDIR /app` | Sets the working directory inside the image |
| `COPY app.py .` | Copies from your machine into the image |
| `ENV APP_VERSION=v1` | Default env var, overridable at runtime |
| `EXPOSE 8080` | **Documentation only** — does not actually publish the port |
| `CMD [...]` | The default command when a container starts. `-u` = unbuffered, so logs appear immediately |

⚠️ **`-u` matters in real life:** Python buffers stdout. Without it, your container logs appear blank until the buffer flushes — a genuinely common "why can't I see my logs" bug.

### Build and run

```bash
docker build -t demo:v1 .
docker images | grep demo

docker run -d --name demo -p 8080:8080 demo:v1
curl localhost:8080
curl localhost:8080/healthz
docker logs demo

# override the env var at runtime — same image, different behavior
docker run --rm -e GREETING=namaste -p 8081:8080 -d --name demo2 demo:v1
curl localhost:8081
docker rm -f demo demo2
```

### Two senior details (10 min, high value)

**1. Layer caching.** Each instruction is a cached layer. Change `app.py`, rebuild:
```bash
echo "# comment" >> app.py
docker build -t demo:v2 .      # watch: earlier steps say CACHED, only COPY onward rebuilds
```
🗣️ *"Layers are cached, so you order a Dockerfile least-changing to most-changing — dependencies before source code. Otherwise every code change re-downloads all your packages."*

**2. Don't run as root.**
```bash
cat > Dockerfile.secure <<'EOF'
FROM python:3.12-slim
RUN useradd -m -u 10001 appuser
WORKDIR /app
COPY app.py .
USER appuser
EXPOSE 8080
CMD ["python", "-u", "app.py"]
EOF

docker build -f Dockerfile.secure -t demo:secure .
docker run --rm demo:secure whoami      # appuser, not root
```
🗣️ *"Containers run as root by default, which is a real risk if something escapes. I add a non-root user and run with USER — same idea as a `runAsNonRoot` securityContext in Kubernetes."*

✅ **Checkpoint:** you built an image from your own Dockerfile and got JSON back from `curl`. **That's a true sentence for Friday.**

---

## Block 3 (20 min) — Create a real Kubernetes cluster

```bash
kind create cluster --name lab
```

Takes ~1 minute. **You now have a real Kubernetes cluster running on your Mac.**

```bash
kubectl cluster-info --context kind-lab
kubectl get nodes                    # 1 node, STATUS Ready
kubectl get nodes -o wide            # OS, container runtime, K8s version
kubectl get pods -A                  # the control plane itself
```

That last command is worth studying. You'll see:

| Pod | What it is |
|---|---|
| `kube-apiserver` | The front door — everything goes through here; only it talks to etcd |
| `etcd` | The database holding all cluster state |
| `kube-scheduler` | Decides which node each pod runs on |
| `kube-controller-manager` | The reconciliation loops that drive actual state → desired state |
| `kube-proxy` | Programs iptables so Service IPs route to pods |
| `coredns` | In-cluster DNS |

> 🧠 **What kind is:** "Kubernetes IN Docker." Each node is itself a Docker container. Prove it: `docker ps` — you'll see `lab-control-plane`. Real Kubernetes, just nodes-as-containers.

🗣️ **Interview line:** *"I built a kind cluster locally — it runs the real control plane, so you can see the API server, etcd, scheduler, controller manager, and kube-proxy running as pods in kube-system."*

---

## Block 4 (40 min) — Deploy and inspect ⭐⭐

Start with nginx so you separate "learning Kubernetes" from "did my image work."

```bash
kubectl create deployment web --image=nginx --replicas=3
kubectl get deployments
kubectl get replicasets
kubectl get pods
kubectl get pods -o wide            # which node, pod IP
```

> 🧠 **The hierarchy:** Deployment → ReplicaSet → Pods. You told Kubernetes "3 replicas"; the Deployment made a ReplicaSet, and the ReplicaSet made 3 Pods. **You never create pods directly in production.**

### The three commands you'll use in every interview answer

```bash
POD=$(kubectl get pods -l app=web -o jsonpath='{.items[0].metadata.name}')

kubectl describe pod $POD           # ← read the Events at the bottom. This is 80% of debugging.
kubectl logs $POD
kubectl get events --sort-by=.lastTimestamp
```

### Watch self-healing happen (do this — it's the core idea)

```bash
kubectl get pods                    # note the names
kubectl delete pod $POD
kubectl get pods                    # a NEW pod appears within seconds
```
🗣️ *"That's the reconciliation loop — I declared 3 replicas, the ReplicaSet controller noticed actual state didn't match desired state and created a replacement. Nothing imperative happened; the controller just converged."*

### Services and DNS

```bash
kubectl expose deployment web --port=80 --name=web-svc
kubectl get svc
kubectl get endpoints web-svc       # ← the actual pod IPs behind the Service

# reach it from your Mac
kubectl port-forward svc/web-svc 8888:80
# in another terminal:
curl localhost:8888
# Ctrl-C to stop

# reach it from INSIDE the cluster by DNS name
kubectl run tmp --rm -it --image=busybox --restart=Never -- sh
  wget -qO- http://web-svc
  nslookup web-svc
  exit
```
> 🧠 **A Service is a stable name + IP in front of changing pods.** Pods die and get new IPs constantly; the Service tracks them by **label selector** and keeps an Endpoints list of *ready* pods.

### Scale, rolling update, rollback

```bash
kubectl scale deployment web --replicas=5
kubectl get pods

kubectl set image deployment/web nginx=nginx:1.27
kubectl rollout status deployment/web
kubectl get rs                      # old RS scaled to 0, new one to 5

kubectl rollout history deployment/web
kubectl rollout undo deployment/web
kubectl rollout status deployment/web
```
🗣️ *"Rollback is instant because the old ReplicaSet is retained — `rollout undo` just scales it back up. In an incident that's my first move: restore service, then root-cause from the events and logs."*

### Other essentials

```bash
kubectl exec -it $POD -- bash        # shell inside a pod
kubectl top nodes                    # may need metrics-server; fine if it errors
kubectl get all                      # everything in the namespace
kubectl explain deployment.spec      # built-in docs — genuinely useful
```

✅ **Checkpoint:** you deployed, scaled, exposed, DNS-resolved, updated, and rolled back a workload. That's real workload-level Kubernetes operations.

---

## Block 5 (25 min) — Deploy YOUR image ⭐

Here's the concept that trips everyone up.

```bash
cd ~/k8slab
kubectl create deployment demo --image=demo:v1
kubectl get pods -l app=demo
```

It will show **`ErrImagePull`** or **`ImagePullBackOff`**. 🎉 **This is a feature of the lab, not a bug.**

```bash
kubectl describe pod -l app=demo | tail -20
```
The Events say it tried to pull `demo:v1` from Docker Hub and failed. **Why:** your image only exists in *your Mac's* Docker. The kind node is a separate container with its own image store. It has no idea your image exists.

**The fix:**
```bash
kind load docker-image demo:v1 --name lab
kubectl delete pod -l app=demo         # force a fresh pull attempt
kubectl get pods -l app=demo -w        # watch until Running (Ctrl-C to stop)
```

Now expose and test:
```bash
kubectl expose deployment demo --port=8080 --name=demo-svc
kubectl port-forward svc/demo-svc 9090:8080
# another terminal:
curl localhost:9090
curl localhost:9090/healthz
```

You should get your JSON back — **your own code, your own image, running in Kubernetes.**

```bash
kubectl logs -l app=demo               # your Python print statements
kubectl scale deployment demo --replicas=3
kubectl port-forward svc/demo-svc 9090:8080
# curl repeatedly — the "pod" field changes as the Service load-balances
```

🗣️ **Interview gold — this is a genuinely good story:** *"First thing I hit deploying my own image to kind was ImagePullBackOff, because the kind node has its own image store separate from my local Docker — you have to `kind load docker-image`. Good lesson in how image resolution actually works: registry, tag, and pull policy all matter, and `imagePullPolicy: Always` with the `latest` tag will bite you the same way."*

---

## Block 6 (15 min) — Lab log + talking points

**Do not skip this.** Specifics are what make the story credible.

```bash
cat > ~/k8slab/LABLOG.md <<'EOF'
# K8s Lab Log

## Tue Aug 25 — Docker + kind
- Colima runtime on macOS; installed docker, kubectl, kind
- Wrote Dockerfile: python:3.12-slim base, WORKDIR, COPY, ENV, EXPOSE, CMD with -u for unbuffered logs
- Built demo:v1; ran with -p and -e to override env at runtime
- Observed layer caching: changing app.py only invalidated COPY onward
- Built a non-root variant (useradd + USER) — maps to runAsNonRoot securityContext
- kind create cluster: saw control plane as pods (apiserver, etcd, scheduler,
  controller-manager, kube-proxy, coredns); nodes are Docker containers
- Deployment -> ReplicaSet -> Pods; deleted a pod and watched the controller
  recreate it (reconciliation loop)
- Service + Endpoints; port-forward from host; in-cluster DNS via busybox wget/nslookup
- Scaled 3->5; rolling update to nginx:1.27; rollout status/history; rollout undo
- HIT: ImagePullBackOff on my own image. Cause: kind node has a separate image
  store from local Docker. Fix: kind load docker-image demo:v1 --name lab
- Deployed my own image, scaled to 3, confirmed Service load-balancing via the
  "pod" field in the JSON response

### Commands that did the real work
kubectl describe pod (Events + Last State) | kubectl logs [--previous]
kubectl get events --sort-by=.lastTimestamp | kubectl rollout undo
EOF
```

### Say these out loud once before bed

1. *"I built a kind cluster this week and deployed my own containerized app into it — Dockerfile through to a Service with load balancing across replicas."*
2. *"Deployment creates a ReplicaSet which creates Pods. I deleted a pod and watched the controller recreate it — that's the reconciliation loop, and it's why Kubernetes is declarative rather than imperative."*
3. *"The first real failure I hit was ImagePullBackOff, and the cause was image resolution — the kind node's image store is separate from local Docker."*
4. *"For debugging, my sequence is `get pods` for status and restart count, `describe pod` for Events and Last State, then `logs --previous` for the container that actually died."*

---

## 🔧 Troubleshooting

| Problem | Fix |
|---|---|
| `Cannot connect to the Docker daemon` | `colima status` → `colima start` |
| `docker: command not found` | `brew install docker` (the CLI is separate from the runtime) |
| kind create hangs or fails | `colima stop && colima start --cpu 4 --memory 8` — kind needs real RAM |
| `ErrImagePull` on your image | `kind load docker-image demo:v1 --name lab`, then delete the pod |
| Pod `Pending` forever | `kubectl describe pod` → Events. Usually resources or scheduling |
| `port-forward` "address already in use" | Pick another local port: `9091:8080` |
| `kubectl` targeting the wrong cluster | `kubectl config use-context kind-lab` |
| `kubectl top` errors | Normal — kind has no metrics-server by default. Skip it |
| Everything is broken | `kind delete cluster --name lab && kind create cluster --name lab` — cheap and fast |

**Don't delete the cluster when you're done — you'll use it tomorrow.**

---

## ✅ Exit test (10 min, no notes)

- [ ] Explain the difference between an image and a container
- [ ] Name all 6 Dockerfile instructions you used and what each does
- [ ] Explain Deployment → ReplicaSet → Pod
- [ ] Say what happens when you `kubectl delete pod` on a managed pod, and why
- [ ] Explain what a Service does and how it finds its pods
- [ ] Recall the 3-command debug sequence
- [ ] Explain why `ImagePullBackOff` happened with your own image

**7 for 7 = tonight worked.** You've moved from "conceptual Kubernetes" to "I built and operated this," which is precisely the gap Matthew flagged.

---

**Tomorrow (Wed, 4h):** hand-written YAML manifests — Deployment, Service, ConfigMap, Secret, Job, CronJob — plus liveness/readiness/startup probes, resource requests and limits, and namespaces. Then **Thursday is the failure lab**, where you break all of it on purpose. That's the day that produces your best interview stories.

Ping me when you finish tonight, or if you hit a wall. And paste the invite/JD whenever it lands — I'll re-cut Thursday and Friday around it.
