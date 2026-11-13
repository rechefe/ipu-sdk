# Workflow

How a feature (a firmware driver, a host feature, a kernel scenario, a loader step) goes from an idea to merged code.

## Steps

1. **Grill.** Run `/grill-with-docs` on the feature with the user. Resolved terms go to `GLOSSARY.md`, decisions to `docs/adr/`.
2. **Spec.** Run `/to-spec`. It publishes a GitHub issue.
3. **Tickets.** Run `/to-tickets` on the spec. Each ticket carries the acceptance criteria below.
4. **Build.** Agents take the tickets, one branch and one PR per ticket. The user merges them one at a time. Each later PR merges `main` and passes again before its turn.

## Acceptance criteria of every ticket

- Mock tests cover the behaviour through the public interface, and pass on every supported target.
- If the feature touches the hardware, a board test covers it. It runs on the lab PC with `bazel test --config=board` and passes there, or the PR says it is pending a board run.
- Expected values come from the target's map, the specs, or `ipu-emulator`; never from the code under test.
- The agent verified the PR from a fresh `git worktree add` of the pushed branch, then removed that worktree.
- On the cluster, every Bazel command names its targets and runs through `qbz`, never `//...`.
