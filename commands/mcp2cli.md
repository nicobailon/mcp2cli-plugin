---
argument-hint: <mcp-package>
description: Convert an MCP server to CLI tools (runs in subagent)
---

# Convert MCP Server to CLI Tools

Spawn a subagent to convert the specified MCP server into standalone Bash-invokable scripts.

## Input

Package: `$ARGUMENTS`

## Execution

Use the **Task tool** to spawn a subagent:

```
Task(
  subagent_type: "general-purpose",
  description: "Convert MCP server to CLI tools",
  prompt: <full prompt below with $ARGUMENTS substituted>
)
```

### Subagent Prompt

Convert the MCP server "$ARGUMENTS" into standalone CLI tools.

**Step 1 - Derive names:**
- Server: Remove @scope/, @version, -mcp suffix, mcp- prefix
- Example: @anthropic-ai/chrome-devtools-mcp@1.0.0 -> chrome-devtools

**Step 2 - Fetch description (parallel with discovery):**
- npm: `curl -s "https://registry.npmjs.org/<pkg>" | jq -r '.readme // .description'`
- PyPI: `curl -s "https://pypi.org/pypi/<pkg>/json" | jq -r '.info.description // .info.summary'`
- Extract first meaningful paragraph (>20 chars)

**Step 3 - Discover tools (auto-fallback):**
```bash
# Try npm first
npx mcporter list --stdio "npx -y <pkg>@latest" --name <server> --schema --json

# If npm fails, try uvx (Python)
npx mcporter list --stdio "uvx <pkg>" --name <server> --schema --json
```

**Step 4 - Group tools:** Max 5-6 per wrapper, dedicated for complex tools

**Step 5 - Generate wrappers:** Node.js ES modules with:
- #!/usr/bin/env node
- import { execSync } from "child_process"
- --help showing ALL parameters
- callMcp() using the command that worked (npx or uvx)

**Step 6 - Write to ~/agent-tools/<server>/ and symlink:**
- package.json (type: module)
- README.md
- .js wrappers (chmod +x)
- Create symlinks in ~/.local/bin for direct invocation:
  ```bash
  mkdir -p ~/.local/bin
  for f in ~/agent-tools/<server>/*.js; do
    ln -sf "$f" ~/.local/bin/$(basename "$f")
  done
  ```

**Step 7 - Register to ~/.claude/CLAUDE.md:**
```markdown
### <Server Name>
<fetched description>

Usage:
```bash
<server>-<tool1>.js --help
<server>-<tool2>.js --param "value"
```

Full docs: `~/agent-tools/<server>/README.md`
```
(Use actual generated filenames in registration)

**Report:** source (npm/PyPI), description, tools count, wrappers, symlinks created, CLAUDE.md confirmation.
