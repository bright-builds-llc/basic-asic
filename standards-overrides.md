# Standards Overrides

Use this file to record deliberate deviations from the canonical coding and architecture standards.

## Active overrides

| Standard | Local decision | Rationale | Owner | Review date |
| --- | --- | --- | --- | --- |
| `Run relevant repo-native verification steps before commit` | `Do not require local shfmt conformance for Bright Builds managed file scripts/bright-builds-auto-update.sh until the upstream managed template matches local formatter output or the install flow documents an explicit exemption` | `The file is upstream-managed, bash -n passes, and reformatting it locally would create avoidable drift from the Bright Builds downstream install source` | `peterryszkiewicz` | `2026-04-30` |

## Notes

- Prefer narrow, explicit exceptions over broad "this repo is different" statements.
- If local verification is intentionally hook-owned or leaves heavy suites to CI, record that explicitly here.
- Revisit overrides periodically instead of letting them become permanent by accident.
- If an override becomes common across many repos, move it back upstream into the canonical standards repo.
