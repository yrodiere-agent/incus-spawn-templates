# incus-spawn-templates

Community template images and tool definitions for [incus-spawn](https://github.com/Sanne/incus-spawn).

These extend the built-in image chain (`tpl-minimal` → `tpl-dev` → `tpl-java`) with project-specific environments that include pre-cloned repositories, build tools, and primed dependency caches.

## Setup

Add this directory as a search path in `~/.config/incus-spawn/config.yaml`:

```yaml
searchPaths:
  - /path/to/incus-spawn-templates
```

Then build and branch:

```shell
isx build tpl-quarkus
isx branch tpl-quarkus my-feature
```

## Templates

All templates inherit the `tpl-java` chain (JDK 25, Maven, Claude Code, GitHub CLI, tmux) through two intermediate base layers:

| Template | Parent | Description |
|----------|--------|-------------|
| `tpl-mydev-base` | `tpl-java` | Base dev environment: Podman, IntelliJ IDEA backend, Gradle |
| `tpl-mydev` | `tpl-mydev-base` | Personal dev environment with Zsh, host resources, settings |
| `tpl-quarkus` | `tpl-mydev` | Quarkus + quickstarts source |
| `tpl-quarkus-aot` | `tpl-quarkus` | Quarkus AOT experiments (includes Hibernate ORM and Models) |
| `tpl-quarkus-infra` | `tpl-mydev` | Quarkus infrastructure projects (lottery, bot, search, GitHub app/action/api) |
| `tpl-hibernate-orm` | `tpl-mydev` | Hibernate ORM source, Podman |
| `tpl-hibernate-reactive` | `tpl-hibernate-orm` | Hibernate Reactive source (includes ORM) |
| `tpl-wildfly` | `tpl-mydev` | WildFly and WildFly Core source |
| `tpl-tamboui` | `tpl-mydev` | TamboUI TUI framework source, selected skills |
| `tpl-incus-spawn` | `tpl-mydev` | incus-spawn itself — recursive development with Incus and COPR tools |
| `tpl-tools` | `tpl-mydev` | Development tools (shell-tools, git-tools, activity-report) |
| `tpl-websites` | `tpl-mydev` | Hibernate websites (hibernate.org, in.relation.to, beanvalidation.org) |

## Tools

| Tool | Description |
|------|-------------|
| `atlassian-mcp` | Atlassian MCP server for Jira and Confluence |
| `code-assistant` | Smart code assistant launcher — resumes the most recent AI coding session |
| `firewalld` | Firewall with permissive defaults for container networking |
| `incus` | Nested Incus daemon for recursive development |
| `opencode` | Open source AI coding agent (opencode.ai) |
| `pinchtab` | [PinchTab](https://github.com/pinchtab/pinchtab) browser control for agents — headless Chromium, the `pinchtab` skill (params: `allowed_domains`, `mcp`) |
| `podman-full` | Podman with compose and permissive short image name resolution |
| `sdkman` | SDKMAN! — manager for JVM ecosystem tools |
| `sdkman-java` | OpenJDK installed via SDKMAN! |
| `sdkman-jbang` | JBang — run Java scripts and apps without build tools |
| `sdkman-maven` | Apache Maven installed via SDKMAN! |
| `sdkman-mvnd` | Maven Daemon (mvnd) for faster Maven builds |
| `sdkman-quarkus` | Quarkus CLI installed via SDKMAN! |
| `zsh` | Zsh as default shell |

## Contributing

To add a new template, create a YAML file in `images/` — see the [incus-spawn documentation](https://github.com/Sanne/incus-spawn) for the image definition format. Custom tools go in `tools/`.
