# Machines

| | Cluster (`enicsw*`) | Lab PC (`enics-ThinkStation-P5`) |
|---|---|---|
| Board | none | U55C, `xilinx_u55c_gen3x16_xdma_base_3`, programmed through `q55` |
| Bazel | yes, through `qbz` | yes, plain `bazel` (bazelisk, user-level) |
| Python | Bazel's hermetic 3.12 | Bazel's hermetic 3.12; `pyxrt` from XRT (built for CPython 3.12) |
| RISC-V GCC | Bazel-downloaded toolchain | the same Bazel-downloaded toolchain |
| Runs | builds, mock tests | builds, mock tests, board tests (`--config=board`) |
| Link-check hook | none: board tests skip | `q55` |
| Clone | `/space/soc/leo_i/rechefe/ipu-fpga/repos/ipu-sdk` | `~/ipu/ipu-sdk` |

## Cluster

Run Bazel only as `qbz <clone> build|test|run <named targets>`. Never `//...`. `qbz` caps CPUs and shares the output root; a raw `bazel` call there can overload a monitored, shared server.

## Lab PC

Board tests run with `bazel test --config=board <targets>`. The `board` config:

1. sources `/opt/xilinx/xrt/setup.sh` if `XILINX_XRT` is unset;
2. runs the test under `sg render` if the shell lacks the `render` group;
3. runs the link check first.

## Link check

Before any board test or tool touches the card, it runs the machine's link-check hook. The hook answers one question: can we use the FPGA now, with this target's xclbin? Board tests skip and tools refuse unless it passes.

The hook is chosen per machine in a site config outside the repo (TODO: path and format, decided in the SDK grilling). On the lab PC the hook asks `q55` that the card is held by us, the shell is up and the target's xclbin UUID is loaded. A machine with no hook configured has no board: board tests skip.
