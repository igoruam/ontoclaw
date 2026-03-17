# OntoClaw MCP Server

Rust-based local MCP (Model Context Protocol) server for OntoClaw.

## Scope

The MCP server is intentionally focused on:

- skill discovery from compiled ontologies
- semantic lookup by intent, dependency, and state transitions
- planning support from `requiresState` and `yieldsState`
- payload retrieval for the calling agent

The server does **not** execute skill payloads. Payload execution is delegated to the calling agent in its current runtime context.

## Transport

The server speaks MCP over `stdio`.

## Ontology Source

The server loads the global compiled ontology catalog from a directory containing `.ttl` files such as:

- `ontoclaw-core.ttl`
- `index.ttl`
- skill modules under nested `skill.ttl` files

Default ontology root:

- auto-discovered by looking for `semantic-skills/` from the current directory and its parents
- fallback: `./semantic-skills`

Override with:

- `--ontology-root /path/to/semantic-skills`
- or `ONTOCLAW_MCP_ONTOLOGY_ROOT=/path/to/semantic-skills`

## Implemented Tools

- `list_skills`
- `find_skills_by_intent`
- `get_skill`
- `get_skill_requirements`
- `get_skill_transitions`
- `get_skill_dependencies`
- `get_skill_conflicts`
- `find_skills_yielding_state`
- `find_skills_requiring_state`
- `check_skill_applicability`
- `plan_from_intent`
- `get_skill_payload`

## Run

Simple run from repository root:

```bash
cargo run --manifest-path mcp/Cargo.toml
```

Explicit path when needed:

```bash
cargo run --manifest-path mcp/Cargo.toml -- --ontology-root ./semantic-skills
```

## Claude Code Integration

You can register the OntoClaw MCP server in Claude Code using the local stdio transport.

Example from the repository root:

```bash
claude mcp add ontoclaw -- \
  cargo run --manifest-path /absolute/path/to/ontoclaw/mcp/Cargo.toml -- \
  --ontology-root /absolute/path/to/ontoclaw/semantic-skills
```

Or, if you want to rely on auto-discovery of `semantic-skills/`:

```bash
claude mcp add ontoclaw -- \
  cargo run --manifest-path /absolute/path/to/ontoclaw/mcp/Cargo.toml
```

After registration, Claude Code can call tools such as:

- `ontoclaw.list_skills`
- `ontoclaw.find_skills_by_intent`
- `ontoclaw.get_skill`
- `ontoclaw.plan_from_intent`
- `ontoclaw.get_skill_payload`

For a full step-by-step guide, see [CLAUDE_CODE_GUIDE.md](CLAUDE_CODE_GUIDE.md).

## Manual MCP Smoke Test

If you want to verify the protocol directly without a client, you can run the server and send MCP `initialize`, `tools/list`, and `tools/call` messages over `stdio`.

The simplest maintained verification path is:

```bash
cd mcp
cargo test
```
