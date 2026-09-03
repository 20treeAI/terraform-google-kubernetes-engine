# Why this fork exists

Upstream has no `agent_sandbox_config` passthrough, so the GKE Agent Sandbox add-on cannot be
enabled through the registry module. This fork carries [upstream PR 2620][pr] on top of the
`v45.0.0` release tag and nothing else.

[pr]: https://github.com/terraform-google-modules/terraform-google-kubernetes-engine/pull/2620

## Branch and tag layout

| Ref | Contents |
| --- | --- |
| `main` | Untouched upstream mirror |
| `overstory/agent-sandbox` | `v45.0.0` + PR 2620 |
| `v45.0.0-agent-sandbox.1` | Immutable tag on that branch, and what tf-infra pins |

PR 2620 adds one variable, `agent_sandbox_enabled`, to the four beta module variants. It is beta
only, so consumers need `google-beta`. The add-on field itself is GA in `hashicorp/google` from
7.39.0, which is also `v45.0.0`'s provider floor.

## Consumers

- `20treeAI/tf-infra` `us-oak-cluster` — the AI agent sandbox cluster (SRE-1527)

## Maintenance

Retire this fork and move the consumer back to the registry once PR 2620 merges upstream.

To pick up a newer upstream release, rebase `overstory/agent-sandbox` onto the new tag and cut the
next `-agent-sandbox.N` tag rather than force-pushing the existing one. `us-oak-cluster` pins an
immutable tag, so a moved tag would change what an already-approved Atlantis plan applies.
