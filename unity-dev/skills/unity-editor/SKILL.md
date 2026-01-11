---
name: unity-editor
description: Unity Editor operations via unity-mcp-client CLI. Handles build verification, console logs, tests, scene/object manipulation. Use after script edits or for debugging.
allowed-tools:
  - Bash(uvx:*)
  - Read
context: fork
agent: general-purpose
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp state 2>/dev/null | grep -q 'success' || echo 'WARNING: Unity Editor connection check - ensure Editor is running' >&2"
          once: true
  Stop:
    - type: command
      command: "uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console --types error --count 5 2>/dev/null || true"
---

# Unity Editor Operations

## Quick Reference

```bash
# Define alias for brevity (all examples below use this)
alias umcp='uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp'
```

## Commands Overview

| Command | Purpose |
|---------|---------|
| `verify` | Full validation (refresh→clear→wait→console) |
| `refresh` | Refresh asset database |
| `state` | Get editor state (isCompiling, isPlaying) |
| `console` | Get console logs |
| `clear` | Clear console |
| `play` / `stop` | Enter/Exit Play Mode |
| `tests <mode>` | Run tests (EditMode/PlayMode) |
| `config` | Show/initialize configuration |
| `scene <action>` | Scene operations |
| `gameobject <action>` | GameObject operations |
| `material <action>` | Material operations |

See [references/mcp-commands.md](references/mcp-commands.md) for detailed options.

## Global Options

| Option | Description | Default |
|--------|-------------|---------|
| `--port` | MCP server port | Auto-detect (macOS) |
| `--host` | MCP server host | localhost |
| `--timeout` | Max wait for compilation (verify) | 5s |
| `--connection-timeout` | TCP connection timeout (verify) | 30s |
| `--retry` | Max connection retry attempts (verify) | 3 |
| `--types` | Log types (error, warning, log) | error warning |
| `--count` | Number of logs to retrieve | 20 |

## Core Workflows

### 1. Build Verification (Post-Edit)

After editing C# scripts, shaders, or .asmdef files:

```bash
umcp verify                        # Standard verification
umcp verify --timeout 120          # Extended timeout for large projects
umcp verify --types error warning log  # Include all log types
```

**Workflow:**
```
Edit Script → verify → Fix if Errors → Repeat
```

### 2. Console Log Analysis

```bash
umcp console --types error         # Errors only
umcp console                       # Errors and warnings (default)
umcp console --types error warning log --count 50  # All logs
```

### 3. Test Execution

```bash
umcp tests EditMode                # Run EditMode tests
umcp tests PlayMode                # Run PlayMode tests
```

### 4. Configuration

```bash
umcp config                        # Show current configuration
umcp config init                   # Generate .unity-mcp.toml
umcp config init --output my.toml  # Custom output path
umcp config init --force           # Overwrite existing
```

### 5. Scene Operations

```bash
umcp scene active                  # Get active scene info
umcp scene hierarchy               # Get scene hierarchy
umcp scene build-settings          # Get scenes in build
umcp scene load --name MainScene   # Load by name
umcp scene load --path Assets/Scenes/Level1.unity
umcp scene load --build-index 0    # Load by build index
umcp scene save                    # Save current scene
umcp scene create --name NewScene --path Assets/Scenes
```

### 6. GameObject Operations

```bash
umcp gameobject find "Main Camera"
umcp gameobject create --name "MyCube" --primitive Cube --position 0,1,0
umcp gameobject modify --name "MyCube" --position 5,0,0 --rotation 0,45,0
umcp gameobject delete --name "MyCube"
```

**Options:** `--name`, `--primitive`, `--position`, `--rotation`, `--scale`, `--parent`, `--search-method`

### 7. Material Operations

```bash
umcp material info --path Assets/Materials/Default.mat
umcp material create --path Assets/Materials/New.mat --shader Standard
umcp material set-color --path Assets/Materials/New.mat --color 1,0,0,1
umcp material assign --path Assets/Materials/New.mat --target "MyCube"
```

### 8. Play Mode Control

```bash
umcp play                          # Enter Play Mode
umcp stop                          # Exit Play Mode
umcp state                         # Check current state
```

## Error Resolution

| Error Type | Identification | Priority |
|-----------|----------------|----------|
| Compilation (CS####) | CS error codes | Critical |
| Assembly Reference | Missing .asmdef refs | Critical |
| Runtime Exception | NullReference, Missing | High |
| Deprecation Warning | Obsolete API usage | Medium |

**Process:**
1. Parse error message (file/line/type)
2. Classify (compilation vs runtime)
3. Apply targeted fix
4. Run `verify` to confirm
5. Iterate until resolved

After 3 failed attempts, ask user for guidance.

## Troubleshooting

**Connection Failures:**
1. Verify Unity Editor is running
2. Check Tools → MCP for Unity → Settings
3. Use `--retry` for transient failures
4. Try `--port 6400` if auto-detection fails

**Timeout Issues:**
1. Increase: `--timeout 120 --connection-timeout 60`
2. Check Unity for blocking dialogs

## Usage Triggers

Use this skill when:
- User reports Unity errors or compilation problems
- After script or asset modifications
- User requests log analysis or debugging
- Before running Unity tests
- Scene or GameObject operations needed
