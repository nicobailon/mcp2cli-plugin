# mcp2cli

Claude Code plugin to convert MCP servers into standalone CLI tools.

## Installation

```bash
/plugin install nicobailon/mcp2cli-plugin
```

Or install locally:
```bash
/plugin install ./path/to/mcp2cli-plugin
```

## Usage

### Slash Command

```bash
/mcp2cli chrome-devtools-mcp
/mcp2cli mcp-server-fetch
/mcp2cli @anthropic-ai/some-mcp-server
```

### Skill (Auto-invoked)

Just describe what you want:
- "Convert chrome-devtools-mcp to CLI tools"
- "Make mcp-server-fetch agent-ready"
- "Wrap the filesystem MCP as bash commands"

## What It Does

1. **Auto-detects** package source (npm or PyPI)
2. **Discovers** all MCP tools via mcporter
3. **Fetches** rich descriptions from npm/PyPI registries
4. **Generates** Node.js CLI wrapper scripts
5. **Creates symlinks** in `~/.local/bin` for direct invocation
6. **Registers** tools to `~/.claude/CLAUDE.md`

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

Symlinks in `~/.local/bin` allow direct invocation:
```bash
chrome-snapshot.js --help
chrome-navigate.js --url "https://example.com"
```

## Requirements

- [mcporter](https://github.com/nicobailon/mcporter) - MCP CLI bridge
- Node.js 18+

## License

MIT
