# mcp2cli

**Claude Code plugin that converts MCP servers into standalone CLI tools—no context bloat, no complex syntax.**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Claude Code Plugin](https://img.shields.io/badge/Claude%20Code-Plugin-blue)]()

```
/mcp2cli chrome-devtools-mcp
```

## Quick Start

1. Install the plugin:
   ```
   /plugin install nicobailon/mcp2cli-plugin
   ```

2. Convert an MCP server:
   ```
   /mcp2cli chrome-devtools-mcp
   ```

3. Use the generated tools:
   ```bash
   chrome-snapshot.js --help
   chrome-snapshot.js --selector "#main"
   ```

## The Problem

### MCP Tool Schemas Bloat Your Context Window

When you install MCP servers in Claude Code, their tool definitions are loaded into the context window *before you send a single message*. A server with 20+ tools can consume thousands of tokens just sitting there. This adds up quickly with multiple servers.

### Raw mcporter Commands Are Not Proactively Used

Even with [mcporter](https://github.com/steipete/mcporter) installed, Claude Code won't proactively use these commands because:
- Complex 100+ char syntax is error-prone
- No `--help` for self-discovery
- Feels "foreign" vs native Bash tools

## The Solution

Convert MCP tools to simple, self-documenting CLI scripts. Claude gets the same functionality via Bash, but:
- Tool schemas don't bloat context (only loaded when `--help` is called)
- Commands are intuitive and short
- Self-documenting via `--help`
- **Schema-aligned**: Script names, parameters, and docs mirror the original MCP schema—agents get the same structured information, but each MCP tool is converted to a bash-invokable script symlinked for easy access

| mcporter CLI | mcp2cli-generated |
|--------------|-------------------|
| 100+ chars, agents fumble | ~50 chars, reliable |
| No `--help` | Full `--help` with examples |
| Abstract entry point | One tool = one script |
| Requires full path | Symlinked for direct invocation |
| Not proactively used | Claude uses reliably |

```bash
# Before: Complex mcporter command Claude won't use proactively
npx mcporter call --stdio "npx -y chrome-devtools-mcp" chrome-devtools.take_snapshot

# After: Simple script Claude uses reliably
chrome-snapshot.js --selector "#main"
```

*For the rationale behind this approach, see [What if you don't need MCP?](https://mariozechner.at/posts/2025-11-02-what-if-you-dont-need-mcp/) by Mario Zechner.*

## How It Works

This is a **prompt-only plugin**—no executable code, just instructions for Claude:

1. You invoke `/mcp2cli <package>` or describe what you want naturally
2. Claude spawns a subagent with detailed conversion instructions
3. Subagent uses mcporter to discover MCP tools
4. Generates Node.js CLI wrappers with `--help` support
5. Creates symlinks in `~/.local/bin` for direct invocation
6. Registers tools to `~/.claude/CLAUDE.md`

## Usage

### Slash Command

```
/mcp2cli chrome-devtools-mcp
/mcp2cli mcp-server-fetch
/mcp2cli @anthropic-ai/some-mcp-server
```

### Skill (Auto-invoked)

Just describe what you want:
- "Convert chrome-devtools-mcp to CLI tools"
- "Make mcp-server-fetch agent-ready"
- "Wrap the filesystem MCP as bash commands"

## Output

Tools are generated to `~/agent-tools/<server-name>/`:
```
~/agent-tools/chrome-devtools/
├── package.json
├── README.md
├── chrome-snapshot.js
├── chrome-navigate.js
└── chrome-click.js
```

Each script is symlinked to `~/.local/bin`, so Claude can invoke by name—no full paths needed:
```bash
# Just the script name works (symlinked)
chrome-snapshot.js --help
chrome-navigate.js --url "https://example.com"
```

## Requirements

- [mcporter](https://github.com/steipete/mcporter) - MCP CLI bridge
- Node.js 18+

## Troubleshooting

| Issue | Fix |
|-------|-----|
| mcporter not found | Ensure `npx mcporter` works |
| Discovery fails | Server may be Python—mcporter tries uvx automatically |
| Tools not in PATH | Add `~/.local/bin` to your PATH |

## Credits

- [mcporter](https://github.com/steipete/mcporter) - MCP CLI bridge by Peter Steinberger
- [What if you don't need MCP?](https://mariozechner.at/posts/2025-11-02-what-if-you-dont-need-mcp/) - Mario Zechner's post on the rationale
- [MCP](https://modelcontextprotocol.io) - The Model Context Protocol

## License

MIT
