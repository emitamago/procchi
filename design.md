# 📐 Procchi DESIGN.md

> This document outlines the core architecture, responsibilities, and system design of Procchi — a programmable process sandbox engine inspired by containers and Tamagotchis.

---

## 🎯 Purpose

Procchi is not a full container runtime. It is a lightweight, educational tool that simulates container-like behavior using Linux namespaces and process isolation. It is designed to be:

* Embedded as a Go package (library-first)
* Controlled via CLI, API, or external agents (e.g. Kubelet-style)
* Understandable, hackable, and extendable

---

## 🧱 Core Components

### 1. `Pull`

* **Responsibility**: Download or copy a rootfs image (e.g. `alpine.tar.gz`) into a local directory (e.g. `~/.procchi/images`)
* **Result**: Prepares an image for future use in `hatch`

### 2. `Hatch`

* **Responsibility**: Spawns a new process inside an isolated namespace environment and optionally switches rootfs (via `chroot`)
* **Result**: A "Procchi" (isolated process) is born and tracked in memory (and later: state)

### 3. `Feed`

* **Responsibility**: Execute a command inside an existing procchi (by PID or name)
* **Result**: The procchi runs the command as its next action (like eating a meal)

---

🧩 cgroup Integration

Procchi uses cgroup v2 to apply resource limits to each "procchi" process. This introduces real-world container-like constraints and enables hands-on learning with process control.

💡 Why cgroup?

Limit memory, CPU, or number of PIDs

Enforce fair resource usage

Simulate real container behavior

📦 cgroup Operations (planned in internal/cgroups)

Create cgroup for each procchi (e.g. /sys/fs/cgroup/procchi/moka/)

Set resource limits via:

memory.max

cpu.max

pids.max

Attach procchi's PID to cgroup.procs

Clean up on process exit

🧪 Example Limits

echo 100M > /sys/fs/cgroup/procchi/moka/memory.max
echo 1000 1000000 > /sys/fs/cgroup/procchi/moka/cpu.max
echo $$ > /sys/fs/cgroup/procchi/moka/cgroup.procs

🧠 Notes

Procchi will default to lightweight limits (e.g. 50MB memory)

Optional --limit flags in CLI

Cgroup code will support graceful fallback if unsupported

---

## 🔩 Core API (planned in `pkg/procchi`)

```go
// Pulls a rootfs image by name
func Pull(image string) error

// Creates and tracks a new isolated process
func Hatch(name string, image string) (*Pet, error)

// Runs a command inside an existing procchi
func Feed(name string, cmd []string) error
```

### Pet struct (WIP)

```go
type Pet struct {
  Name      string
  PID       int
  Image     string
  CreatedAt time.Time
  Mood      string // optional, for fun
}
```

---

## 🧠 Namespace + syscall usage (per command)

| Command | Syscalls Used                                       | Notes                             |
| ------- | --------------------------------------------------- | --------------------------------- |
| `hatch` | `clone`, `chroot`, `mount`, `sethostname`, `execve` | main isolation and process setup  |
| `feed`  | `execve`, `kill`, `write`                           | execute command or signal process |
| `pull`  | (no syscall)                                        | simple HTTP/file fetch            |

---

## 🗂️ Directory Structure (planned)

```
procchi/
├── cmd/               ← CLI frontend
├── pkg/procchi/       ← Core API (Pull, Hatch, Feed)
├── internal/syswrap/  ← Raw syscall wrappers
├── docs/              ← DESIGN.md, syscalls.md, etc
├── main.go            ← entry point (test CLI or daemon)
```

---

## 🛣️ Roadmap

* [x] README.md (project intent and tone)
* [x} DESIGN.md (this file)
* [ ] Core API stubs and interfaces
* [ ] Internal syscall layer design
* [ ] MVP implementation: Pull → Hatch → Feed
* [ ] CLI wrapping
* [ ] State persistence (PID tracking)
* [ ] Web/API interface (stretch goal)

---

## 🔚 Notes

This document evolves as Procchi does. It's not just design — it's a care manual for your process pets 🐣

