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
