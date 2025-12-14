# Unity MCP Client CLI Reference

## Overview

`unity-mcp-client` is a CLI tool for Unity Editor operations via socket connection. Install and run via `uvx` for always-latest version.

## Installation & Usage

```bash
# Run any command (auto-installs on first use)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp <command>
```

**Port Auto-Detection:** On macOS, the port is automatically detected from Unity EditorPrefs. Manual `--port` is only needed for non-standard setups.

---

## Commands

### verify

**Full validation workflow: refresh → clear → compile wait → console check**

```bash
# Default (60s timeout, 3 retries, error+warning)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify

# Extended timeout for large projects
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify --timeout 120

# More retry attempts
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify --retry 5

# Include all log types
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify --types error warning log
```

**Options:**
| Option | Description | Default |
|--------|-------------|---------|
| `--timeout` | Max wait for compilation (seconds) | 60 |
| `--retry` | Max connection retry attempts | 3 |
| `--types` | Log types to check | error warning |

**Workflow Steps:**
1. Trigger asset refresh
2. Clear console
3. Poll `isCompiling` until false (or timeout)
4. Display console logs

---

### refresh

Trigger Unity asset database refresh.

```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp refresh
```

**Note:** This is async. Use `verify` for complete workflow with wait.

---

### state

Get current Unity Editor state.

```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp state
```

**Response:**
```json
{
  "success": true,
  "data": {
    "isPlaying": false,
    "isPaused": false,
    "isCompiling": false,
    "isUpdating": false,
    "activeSceneName": "MainGame",
    "selectionCount": 1
  }
}
```

---

### console

Get Unity console logs.

```bash
# Default (error + warning, 20 entries)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console

# Errors only
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console --types error

# All types with limit
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console --types error warning log --count 50
```

**Options:**
| Option | Description | Default |
|--------|-------------|---------|
| `--types` | Log types (space-separated) | error warning |
| `--count` | Max entries to retrieve | 20 |

---

### clear

Clear Unity console.

```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp clear
```

---

### play

Enter Unity Play Mode.

```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp play
```

---

### stop

Exit Unity Play Mode.

```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp stop
```

---

### find

Find GameObject in hierarchy.

```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp find "PlayerController"
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp find "Canvas/UI/Button"
```

---

### tests

Run Unity tests.

```bash
# Edit Mode tests
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp tests edit

# Play Mode tests
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp tests play
```

---

## Global Options

| Option | Description | Default |
|--------|-------------|---------|
| `--port` | MCP server port | Auto-detect (macOS) |
| `--host` | MCP server host | localhost |

---

## Common Workflows

### Post-Edit Verification

```bash
# Single command does everything
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify
```

### Quick Error Check

```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console --types error --count 10
```

### Test Execution

```bash
# Verify first, then test
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify && \
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp tests edit
```

---

## Troubleshooting

### Connection Refused

1. Verify Unity Editor is running
2. Check MCP for Unity plugin (Tools → MCP for Unity)
3. Try with explicit port: `--port 6400`

### Timeout During Verify

1. Increase timeout: `--timeout 120`
2. Check Unity for blocking dialogs
3. Manual refresh in Unity (Cmd+R)

### Port Auto-Detection Failed (macOS)

1. Specify manually: `--port 6400`
2. Check Unity EditorPrefs:
   ```bash
   defaults read com.unity3d.UnityEditor5.x "MCPForUnity.UnitySocketPort"
   ```
