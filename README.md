# Astra Luna Orchestrator

**Save Astra for the decisions that need it. Let GPT-5.6 Luna do the volume.**

![Astra Luna Orchestrator measured efficiency](docs/assets/astra-savings-v2.svg)

A personal Codex skill designed to preserve Astra usage without giving up Astra's
judgment. Astra stays responsible for planning, architecture, high-stakes
decisions and final review. GPT-5.6 Luna takes the high-volume work:
repository discovery, implementation, testing, debugging and routine verification.

Bring an existing plan or start with a feature request. The workflow turns it
into coherent implementation bundles, sends those bundles to Luna, then returns
the completed patch and evidence to Astra for one focused acceptance pass.

> **Status:** early release. Offline installation tests pass, and the workflow has completed a measured local field build. Results below describe that run, not guaranteed savings. A new installation still needs runtime routing verification on its first authorized task. Installation never runs paid inference.

## Measured efficiency

In one substantial field build, Astra Luna Orchestrator used **98.9% less Astra
input per 1,000 implementation and test lines** than the all-Astra baseline. It
did that by moving the implementation loop—not the important decisions—to Luna.
Total API-equivalent compute per 1,000 lines was **97.0–97.7% lower**, while the
measured phase produced 39% more implementation and test lines.

| Workflow | Astra input per 1K implementation lines | Total compute per 1K lines |
| --- | ---: | ---: |
| All Astra | 8.56M | $11.32 |
| Astra + GPT-5.6 Luna | **95.9K** | **$0.26–$0.34** |

The per-token price difference explains why delegating implementation has so
much leverage:

| Cost per 1M tokens | Astra estimator | GPT-5.6 Luna | Astra premium |
| --- | ---: | ---: | ---: |
| Uncached input | $10.00 | $0.15–$0.30 | 33–67× |
| Cached input | $1.00 | $0.003–$0.006 | 167–333× |
| Output | $50.00 | $0.60–$1.20 | 42–83× |

Astra does not have a public API SKU; its values above are API-equivalent
estimates, not ChatGPT or Codex subscription charges. Luna values use published
off-peak and peak API rates. See the [benchmark methodology](docs/BENCHMARK.md)
for sources, exact measurements and limitations.

## How it works

```text
Astra  →  scope + design + task brief
Luna  →  implement + test + report
Astra  →  review + verify + accept or request fixes
       →  integrate + checkpoint + next task
```

- **Native delegation:** uses the `astra_luna_builder` role, not a separate agent CLI.
- **Coherent assignments:** one feature slice can include many edit/test/fix steps.
- **Focused Astra root:** normally one planning batch, one dispatch, one wait, one
  batched acceptance review and one final response.
- **Worker-owned execution:** Luna handles in-scope discovery, implementation,
  testing, debugging and routine browser/visual QA without progress polling.
- **Review before acceptance:** the builder submits evidence; Astra decides whether it is complete.
- **Existing plans welcome:** works with repository plans, Superpowers/GSD artifacts, or the included templates.
- **Controlled parallel work:** one writer by default; two only with independent tasks and verified separate workspaces.
- **Reversible installation:** dry run, backups and a guarded undo receipt.

This is workflow guidance, not a deterministic scheduler, a security sandbox, or a guarantee of model quality or cost savings. It is independent of OpenAI, DeepSeek and Codex Router.

### One orchestration workflow

There is no mode setting or mode-switch command. The package always uses the
usage-saving Astra → Luna → Astra workflow for substantial implementation.

Three routing outcomes remain intentionally different:

- Substantial implementation uses Astra to plan and review while Luna builds.
- Trivial work and explicit single-agent requests stay with the root session.
- Concrete security, architecture, payments, tenancy, secrets, migration or
  production risk can justify targeted additional Astra review.

Those are scope and safety decisions, not user-selectable performance modes.

## Requirements

Before installing, you need:

1. A Codex client that supports native subagents and standalone custom agent TOML files under `$CODEX_HOME/agents/`.
2. GPT-6 Astra selected as the root model.
3. Python **3.11 or newer**. No third-party Python dependencies are needed.
4. A local Codex model catalog advertising `gpt-5.6-luna` with `multi_agent_version: "v1"` or `"v2"`.

> **Do not spend API credit during installation.** Installing this package does
> not authorize an assistant to run `subagents certify`, `test-model --live`, a
> paid inference probe. If the selected route is absent or is not advertised for
> native subagents, the installer stops and reports
> the prerequisite. Decide separately whether to certify a route yourself.

Do **not** add or change `[agents].default_subagent_model` for this package. The
installer creates a named `astra_luna_builder` role that pins its own route and
catalog-supported effort, so unrelated subagents keep their existing defaults.
The installer **does not install the Router, add credentials, select your root
model, or rewrite `config.toml`**. It pins direct OpenAI `gpt-5.6-luna` and does
not configure a Router or provider credentials.

## Install

Download this repository as a ZIP and extract it, or clone it:

```sh
git clone https://github.com/vadhh/astra-luna-orchestrator.git
cd astra-luna-orchestrator
```

Run the following commands from that repository folder.

### Fastest safe terminal install

The installer performs its own prerequisite checks before writing. Preview the
exact destinations, then apply:

```sh
python3 -B install.py
python3 -B install.py --apply
```

That is the normal installation path. The first command changes nothing. The
second repeats preflight, installs atomically, backs up existing instructions and
prints a guarded undo receipt. It does not change your root model, Router,
credentials, permissions or reasoning effort.

To use an already-configured alternate provider, pass its exact route to both
commands. For OpenRouter:

```sh
python3 -B install.py --worker-route openrouter/deepseek-v4.1-luna
python3 -B install.py --worker-route openrouter/deepseek-v4.1-luna --apply
```

The option selects an existing catalog route; it does not configure the provider,
collect a key, certify the model or make an inference request.

### With Codex

Ask Codex:

```text
Read INSTALL-IN-CODEX.md in this folder and install the package following it.
Preserve my root model, reasoning effort, Router, config and authentication.
Do not launch workers or run paid inference during installation.
```

### Verify the package locally

Release archives are tested before publication. If you also want to run the
offline suite yourself:

```sh
python3 -B -m unittest discover -s tests -v
```

For a nondefault profile, pass `--profile PROFILE` to the dry run, apply and doctor consistently. `--home` and `--codex-home` are available for explicit location overrides. Use the same locations for undo.

### What changes

| Location | Installed content |
| --- | --- |
| `~/.agents/skills/astra-luna-orchestrator/` | Skill, references, templates, doctor, plan validator and routing binding |
| `$CODEX_HOME/agents/astra_luna_builder.toml` | Native builder pinned to Luna; nested agents disabled |
| `$CODEX_HOME/AGENTS.md` | A marked, scoped workflow policy block |
| `$CODEX_HOME/astra-luna-install-backups/` | Original files and an undo receipt |

`CODEX_HOME` defaults to `~/.codex`. An existing nonempty `AGENTS.override.md` receives the policy instead of `AGENTS.md`. Other instructions are preserved. The policy keeps trivial work single-agent and honors explicit no-delegation requests, repository restrictions and managed policies. Use `--no-policy` for a skill/role-only installation.

Root model/effort, provider configuration, authentication and existing permissions stay unchanged. Installation does not start services, workers or model requests, and does not commit, push or deploy anything.

## Start your first task

**Fully quit and reopen the host app (ChatGPT or Codex), then start an Astra session.** A new chat alone may reuse a cached model catalog. Use:

```text
$astra-luna-orchestrator Use the existing plan in docs/plan.md to implement
this feature. Keep Astra focused on planning and final review. Use one installed
Luna builder for a coherent implementation and verification bundle. Do not poll
the worker; review its completed patch and evidence in one batched pass.
```

Replace the example plan path with your actual plan or describe the feature. Your first authorized useful task should verify the child model and provider using host/router request metadata. A worker saying its model name is not proof.

If the session does not expose the custom role or exact worker model, do not substitute another model or launch a second CLI. Check client support and session configuration first.

## Check your setup

From the repository folder:

```sh
python3 -B skill/astra-luna-orchestrator/scripts/doctor.py
python3 -B skill/astra-luna-orchestrator/scripts/doctor.py --check-local-router
```

An installed copy reads its generated `routing.json`, so doctor checks the same
route automatically. Pass `--worker-route` only when running doctor from a fresh
source checkout or intentionally checking a different reviewed route.

The first checks local configuration/catalog data. The optional second command makes only a local `/models` GET, with proxies and redirects disabled. It does not read authentication files or attach credentials; an authenticated Router may reject it even when normal Codex requests work. Do not disable Router authentication to make this check pass.

Neither check proves paid inference works. See [troubleshooting](docs/TROUBLESHOOTING.md) and [validation evidence](docs/VALIDATION.md).

## Updating and uninstalling

For an update, download the new source, run its tests, and preview `python3 -B install.py --replace`. Review the differences before applying with `--replace --apply`. Existing package-owned files are backed up; unrelated files are not deleted. An existing valid `routing.json` preserves the installed provider when `--worker-route` is omitted. Pass the option explicitly only to change providers, and review that replacement before applying it. Do not edit generated `routing.json` or the agent model to force a different provider through preflight.

Preview undo using the exact receipt printed during installation:

```sh
python3 -B install.py --undo /path/to/receipt.json
```

Add `--apply` to restore. Undo refuses if a managed file changed afterward, protecting later edits. Backups remain available. Keep a copy of the installer and receipt; receipts may contain private paths and original instructions and should never be published.

## Contributing and distribution

- [Contributing](CONTRIBUTING.md): tests, changes and evidence expectations.
- [Security](SECURITY.md): privacy boundaries and safe reporting.
- [Sources](SOURCES.md): provenance and upstream references.
- [Release preparation](docs/RELEASE.md): GitHub description, topics and release checks.
- [Changelog](CHANGELOG.md): changes from the original package.

To validate the synthetic plan example:

```sh
python3 -B skill/astra-luna-orchestrator/scripts/validate_plan.py examples/invoice-filter/plan.json
```

The example is a planning fixture, not a runnable application. Markdown plans work without the optional manifest validator.
