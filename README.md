# Mini-K8s: A Distributed Task Orchestrator in Go

**Mini-K8s** is a lightweight, distributed container orchestrator built from the ground up in Go. It mimics the core architectural patterns of Kubernetes, including a centralized API server, a persistent state store, and autonomous worker nodes (Kubelets) that manage container lifecycles via the Docker API.

## 🧠 Project Philosophy
The goal of Orbit is to demystify "Control Plane" logic by implementing the three pillars of distributed systems:
1. **The Desired State:** What the user wants (e.g., "Run 3 Nginx containers").
2. **The Actual State:** What is currently running on the workers.
3. **The Reconciliation Loop:** The constant logic that moves the Actual State toward the Desired State.

---

## 🏗 System Components

### 1. The Manager (Control Plane)
The brain of the cluster. It consists of:
* **API Server:** A RESTful entry point for submitting "Task" definitions.
* **Scheduler:** Watches for unscheduled tasks and assigns them to nodes based on resource availability (CPU/RAM).
* **Reconciler:** A continuous loop that handles self-healing. If a node heartbeats fail, the Reconciler reschedules its tasks.

### 2. The Worker (Data Plane)
The muscle of the cluster.
* **Kubelet-Lite:** A Go binary running on every node that communicates with the Manager via gRPC.
* **Container Runtime:** Uses the Docker SDK to pull images and manage the lifecycle of "Tasks."

### 3. The State Store (Etcd-Lite)
* Uses a persistent Key-Value store (BoltDB or a simplified Raft implementation) to ensure the cluster can recover from a Manager crash.

---

## 📂 Project Structure
```text
.
├── cmd/
│   ├── manager/          # Entry point for the Control Plane
│   └── worker/           # Entry point for the Node Agent
├── internal/
│   ├── api/              # HTTP handlers for task submission
│   ├── scheduler/        # Bin-packing algorithms for task placement
│   ├── worker/           # Docker SDK integration and heartbeats
│   ├── store/            # Key-Value store logic
│   └── proto/            # gRPC definitions for Manager-Worker comms
├── pkg/
│   └── task/             # Core Task and Node data structures
└── Makefile              # Build and run commands
