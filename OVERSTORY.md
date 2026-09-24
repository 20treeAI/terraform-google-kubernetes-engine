# Why this fork exists

Upstream has no `agent_sandbox_config` or `pod_snapshot_config` passthrough, so neither the GKE
Agent Sandbox add-on nor pod snapshots can be enabled through the registry module. This fork
carries [upstream PR 2620][pr2620] and [upstream PR 2625][pr2625] on top of the `v45.0.0` release
tag and nothing else.

[pr2620]: https://github.com/terraform-google-modules/terraform-google-kubernetes-engine/pull/2620
[pr2625]: https://github.com/terraform-google-modules/terraform-google-kubernetes-engine/pull/2625

## Branch and tag layout

| Ref | Contents |
| --- | --- |
| `main` | Untouched upstream mirror |
| `overstory/agent-sandbox` | `v45.0.0` + PR 2620 + PR 2625 |
| `v45.0.0-agent-sandbox.1` | Immutable tag, PR 2620 only |
| `v45.0.0-agent-sandbox.2` | Immutable tag, PR 2620 + PR 2625, and what tf-infra pins |

PR 2620 adds one variable, `agent_sandbox_enabled`, to the four beta module variants. It is beta
only, so consumers need `google-beta`. The add-on field itself is GA in `hashicorp/google` from
7.39.0, which is also `v45.0.0`'s provider floor.

PR 2625 adds `pod_snapshot_config` (bool, default `false`) to every variant. Its three safer-cluster
`main.tf` hunks were applied by hand: the fork's attribute alignment differs from the PR's base.

## Consumers

- `20treeAI/tf-infra` `us-oak-cluster` — the AI agent sandbox cluster (SRE-1527, SRE-1651)

## Maintenance

Retire this fork and move the consumer back to the registry once PRs 2620 and 2625 merge upstream.

To pick up a newer upstream release, rebase `overstory/agent-sandbox` onto the new tag and cut the
next `-agent-sandbox.N` tag rather than force-pushing the existing one. `us-oak-cluster` pins an
immutable tag, so a moved tag would change what an already-approved Atlantis plan applies.
