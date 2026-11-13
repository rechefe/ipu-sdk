# ipu-sdk

Firmware, host runtime, tests and model loader for the IPU on its hardware targets (`fpga-v2.1`, …). The hardware itself lives in `rechefe/ipu-fpga`; the kernels and the golden model in `rechefe/ipu-emulator`.

## Machines

This repo is worked on from two machines. Check which one you are on (`hostname`) before building or testing. See `docs/agents/machines.md`.

- **Cluster** (`enicsw*`): no board. Builds and runs mock tests. Bazel runs through `qbz` there.
- **Lab PC** (`enics-ThinkStation-P5`): the U55C. Builds, runs mock tests and board tests, with plain `bazel`.

## Board safety (lab PC)

Programming goes through the `q55` tool only, called directly with no wrapper around it. The agent loads the Xilinx shell bitstream and the target's xclbin with `q55` on its own.

Each of these needs the user's explicit OK for that step, every time:

- flashing, rebooting, system package installs, changing the kernel (it stays on 6.17).

Never, under any OK:

- programming by any route other than `q55`: no JTAG (it panics the host), no `xbutil`/`xbmgmt` program;
- powering the machine off (nothing can switch it on remotely);
- `sudo`.

When using the card:

- Every board test and tool first runs the machine's **link check** and operates only if it passes; board tests skip otherwise. The check is a per-machine hook (see `docs/agents/machines.md`); on the lab PC it asks `q55` that the card is held by us, the shell is up and the target's xclbin is loaded.
- Tests never take the card. Taking and giving it back is an explicit step; give it back when done. The q55 queue is shared with other users.
- Only safe accesses: no reads that trip the AXI firewall, no HBM outside your own buffers, no writes to read-only registers.
- Release files stay as shipped; verify their sha256 before use.
- Cap threads (`OMP_NUM_THREADS`) for parallel jobs; the machine is shared.

## Git

Commit locally. Push, open PRs and merge only when the user says so. No CI.

## Agent skills

### Workflow

Grill, spec, tickets, one PR per ticket, and every ticket's acceptance criteria. See `docs/agents/workflow.md`.

### Issue tracker

GitHub Issues on `rechefe/ipu-sdk`, through `gh`. See `docs/agents/issue-tracker.md`.

### Triage labels

The five default labels, plus `hw` for board findings. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: a root `GLOSSARY.md` and `docs/adr/`, both created when first needed. See `docs/agents/domain.md`.
