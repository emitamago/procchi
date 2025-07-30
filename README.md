# 🐣 Procchi

> A container runtime for people who want to raise their processes like digital pets.

---

## 🧠 What is Procchi?

Procchi is **not** just a CLI tool.
It's a lightweight **process isolation engine**, inspired by container runtimes like containerd, but rebuilt with a focus on learning, playfulness, and minimalism.

You can control Procchi via:

* ✅ a built-in CLI (`procchi hatch`, `procchi feed`, etc.)
* ✅ embedding it as a Go package
* 🛠️ future: API or Kubelet-style agents

Procchi lets you "hatch" small isolated processes (called *procchis*) inside new namespaces and rootfs environments — and interact with them like tiny digital pets.

---

## 💡 Why does this exist?

Because:

* I wanted to learn how containers *actually* work (clone, chroot, etc.)
* I believe in learning through building weird things
* I wanted something that feels like *a container runtime Tamagotchi*

Procchi is not production-grade.
But it's real enough to make you go “ohhhh so *that’s* what `runc` is doing”.

---

## 🐣 MVP Usage

```bash
procchi pull alpine
# ⤷ Download a simple rootfs image to ~/.procchi/images

procchi hatch moka --image alpine
# ⤷ Start a process called "moka" in a new namespace and rootfs

procchi feed moka echo "hi!"
# ⤷ Feed moka a command to run
```

That’s it.
This is your minimal “container-like experience” — no Docker, no OCI, just pure syscalls and imagination.

---

## 🧩 Architecture

```
┌────────────┐        CLI (Cobra)
│  procchi   │────┐
└────────────┘    │
                  ▼
         ┌──────────────────┐
         │  Procchi Core API │  ← importable Go pkg
         └──────────────────┘
                  ▲
     future: Kubelet-like controller / daemon
```

* `cmd/` = CLI interface
* `pkg/procchi/` = reusable logic (Hatch, Pull, Feed, etc.)
* `internal/` = syscall-level logic (namespace, chroot, etc.)

---

## 🛠️ Features (so far)

* ✅ Namespace isolation (PID, UTS, etc.) via `clone()`
* ✅ `chroot()` into downloaded rootfs (like alpine)
* ✅ Lightweight CLI
* ✅ Designed as a **library-first** system

---

## 🌱 Future ideas

* `procchi list` – show active procchis (and their moods?)
* `procchi hug` – send them love (SIGCONT)
* `procchi logs` – show what they’ve pooped out (stdout)
* API server – command your procchis via HTTP/gRPC
* `procchi evolve` – give them bash and watch them grow\...

---

## 📚 What I learned

* How Linux containers are made of syscalls and illusion
* The joy of `unshare`, `pivot_root`, `procfs`, and other dark arts
* Why real runtimes like containerd are amazing (and terrifying)
* That making containers cute helps me learn more

---

## 📝 License

MIT, obviously.
Just don't run Kubernetes on this, please. Or do. I'm not your boss.

