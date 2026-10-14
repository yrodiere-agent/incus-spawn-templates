# incus-spawn-templates

Custom image and tool definitions for [incus-spawn](https://github.com/Sanne/incus-spawn) (`isx`). Registered as a search path in `~/.config/incus-spawn/config.yaml`.

## Repo Layout

```
images/          # Image definitions (YAML)
tools/           # Tool definitions (YAML)
files/           # Static files deployed into containers via host-resources (mode: copy)
```

## How to Write Images and Tools

The incus-spawn README is the authoritative reference:

- [Template Images](https://github.com/Sanne/incus-spawn#template-images) — image schema, inheritance, building
- [Custom Tools](https://github.com/Sanne/incus-spawn#custom-tools) — tool schema, execution order, downloads, files
- [Environment Variables](https://github.com/Sanne/incus-spawn#environment-variables) — structured env entries with merge strategies
- [Proxy Credentials](https://github.com/Sanne/incus-spawn#proxy-credentials) — MITM proxy credential injection for tools
- [Built-in Tools](https://github.com/Sanne/incus-spawn#built-in-tools) — tools that ship with isx (`podman`, `claude`, `gh`, etc.)
- [Host Resources](https://github.com/Sanne/incus-spawn#host-resources) — sharing host files/directories with containers
- [Tool Parameters](https://github.com/Sanne/incus-spawn#tool-parameters) and [Tool Actions](https://github.com/Sanne/incus-spawn#tool-actions)

Use `isx tools show <name>` to inspect any tool's definition, and `isx templates list -v` to see all available templates.

## Resolution Order

Both images and tools are discovered from four layers (later overrides earlier by `name`):

1. **Built-in** — bundled with isx
2. **User** — `~/.config/incus-spawn/{images,tools}/`
3. **Search paths** — directories in `config.yaml` `searchPaths` (this repo)
4. **Project-local** — `.incus-spawn/{images,tools}/`

## Inspecting What's Available

Run `isx templates list -v` for all templates (built-in + custom) with their parent chain, and `isx tools list -v` for all tools.

## Template Authoring Conventions

### Host-only repositories

For a repo that lives on the host but must not be reached over the network (VPN-gated, credential-gated, proprietary), don't overlay-mount it directly at its final path. Instead:

1. `host-resources` with `mode: overlay` stages it at `~/.host-repos/<name>`
2. A `repos:` entry with `url: file:///home/agentuser/.host-repos/<name>` clones from that staging path into the real working directory `~/<name>`

This gives full isx repo integration (independent git metadata, prime support, trust confinement) instead of an opaque raw mount. See `hibernate-orm-product.yaml` as the reference implementation.

Such templates should inherit from `tpl-dev-secure` (Bob-only AI tooling), since they handle proprietary or sensitive code.

### Stale host-repo warnings

Any template using the host-only-repo pattern must include an `agent_note` that instructs the agent to check the last commit date of each host repo and, if any is more than ~7 days old, stop and confirm with the user before doing real work. The host-repo snapshot is taken at image build time, so it can go stale silently.

### Tool conflicts

Don't add `podman-custom` to a template that inherits `podman` from `tpl-dev-base` — both set `DOCKER_HOST` differently and the conflict causes build failures. Always verify a tool doesn't conflict with inherited tools before adding it.

### Build-testing

Always build-test new or changed templates against real Incus (`isx build <template>` from a `tpl-incus-spawn` instance) rather than relying on YAML/schema validation alone. A schema-valid template can still fail at build time — this caught a real conflict (the `podman-custom` issue above) that validation missed.

### Working on the agent fork

When branching off the agent fork (`yrodiere-agent/incus-spawn-templates`) to work on this repo, branch off `upstream/main` (`yrodiere/incus-spawn-templates`), not the fork's own `main` — the fork is often stale and branching off it produces a noisy diff full of unrelated drift.
