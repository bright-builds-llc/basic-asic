<!-- coding-and-architecture-requirements-managed:begin -->
# Bright Builds Standards

`AGENTS.md` is the entrypoint for repo-local instructions, not the complete Bright Builds specification.

Before plan, review, implementation, or audit work:

1. Read the repo-local instructions in `AGENTS.md`, including any `## Repo-Local Guidance` section and any instructions outside this managed block.
2. Read `AGENTS.bright-builds.md`.
3. Read `standards-overrides.md` when present.
4. Read the pinned canonical standards pages relevant to the task.
5. If you have not done that yet, stop and load those sources before continuing.

- Keep recurring repo-specific workflow facts, commands, and links in a `## Repo-Local Guidance` section elsewhere in this file.
- Record deliberate repo-specific exceptions and override decisions in `standards-overrides.md`.
- If instructions elsewhere in `AGENTS.md` conflict with `AGENTS.bright-builds.md`, follow the repo-local instructions and treat them as an explicit local exception.
<!-- coding-and-architecture-requirements-managed:end -->

## Repo-Local Guidance

- During substantive work, especially GSD planning and execution tasks, review
  whether relevant docs such as `README.md`, `.planning/` docs,
  `CONTRIBUTING.md`, or other contributor-facing or user-facing docs have
  become incomplete or misleading.
- When a change materially affects project understanding, workflow, roadmap,
  contributor expectations, or public-facing positioning, update the relevant
  docs in the same change.
- Do not churn docs gratuitously when the work does not materially affect
  documentation.
