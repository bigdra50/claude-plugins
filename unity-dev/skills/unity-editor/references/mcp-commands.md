# Unity MCP Client CLI Reference

## Quick Setup

```bash
# Define alias for brevity
alias umcp='uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp'
```

**Port Auto-Detection:** On macOS, port is auto-detected from Unity EditorPrefs.

---

## Commands

### verify

Full validation: refresh → clear → compile wait → console check

```bash
umcp verify                              # Default (5s timeout, 3 retries)
umcp verify --timeout 120                # Extended timeout
umcp verify --connection-timeout 60      # Extended connection timeout
umcp verify --retry 5                    # More retry attempts
umcp verify --types error warning log    # Include all log types
```

| Option | Description | Default |
|--------|-------------|---------|
| `--timeout` | Max wait for compilation (seconds) | 5 |
| `--connection-timeout` | TCP connection timeout (seconds) | 30 |
| `--retry` | Max connection retry attempts | 3 |
| `--types` | Log types to check | error warning |

---

### config

Show or initialize configuration.

```bash
umcp config                        # Show current configuration
umcp config init                   # Generate .unity-mcp.toml
umcp config init --output my.toml  # Custom output path
umcp config init --force           # Overwrite existing file
```

**Configuration file format (.unity-mcp.toml):**
```toml
port = 6401
host = "localhost"
timeout = 5.0
connection_timeout = 30.0
retry = 3
log_types = ["error", "warning"]
log_count = 20
```

---

### refresh

Trigger Unity asset database refresh (async).

```bash
umcp refresh
```

Use `verify` for complete workflow with wait.

---

### state

Get current Unity Editor state.

```bash
umcp state
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
umcp console                                    # Errors and warnings (default)
umcp console --types error                      # Errors only
umcp console --types error warning log --count 50  # All types with limit
```

| Option | Description | Default |
|--------|-------------|---------|
| `--types` | Log types (space-separated) | error warning |
| `--count` | Max entries to retrieve | 20 |

---

### clear

Clear Unity console.

```bash
umcp clear
```

---

### play / stop

Enter or exit Unity Play Mode.

```bash
umcp play    # Enter Play Mode
umcp stop    # Exit Play Mode
```

---

### tests

Run Unity tests.

```bash
umcp tests EditMode    # Run EditMode tests
umcp tests PlayMode    # Run PlayMode tests
```

---

### scene

Scene operations.

| Action | Purpose | Required Options |
|--------|---------|------------------|
| `active` | Get active scene info | None |
| `hierarchy` | Get scene hierarchy tree | None |
| `build-settings` | Get scenes in build | None |
| `load` | Load scene | `--name`, `--path`, or `--build-index` |
| `save` | Save current scene | `--path` (optional) |
| `create` | Create new scene | `--name` (required), `--path` (optional) |

```bash
umcp scene active
umcp scene hierarchy
umcp scene build-settings
umcp scene load --name MainScene
umcp scene load --path Assets/Scenes/Level1.unity
umcp scene load --build-index 0
umcp scene save
umcp scene create --name NewScene --path Assets/Scenes
```

---

### gameobject

GameObject operations.

| Action | Purpose | Required Options |
|--------|---------|------------------|
| `find` | Find GameObject by name | `<name>` argument |
| `create` | Create new GameObject | `--name` (required) |
| `delete` | Delete GameObject | `--name` (required) |
| `modify` | Modify transform | `--name` (required) |

**Options:**
| Option | Description |
|--------|-------------|
| `--name` | Object name |
| `--primitive` | Cube, Sphere, Capsule, Cylinder, Plane, Quad |
| `--position` | x,y,z format (e.g., `0,1,0`) |
| `--rotation` | x,y,z format |
| `--scale` | x,y,z format |
| `--parent` | Parent object name |
| `--search-method` | Search method (default: by_name) |

```bash
umcp gameobject find "Main Camera"
umcp gameobject create --name "MyCube" --primitive Cube --position 0,1,0
umcp gameobject create --name "Player" --parent "GameManager" --position 1,2,3
umcp gameobject modify --name "MyCube" --position 5,0,0 --rotation 0,45,0 --scale 2,2,2
umcp gameobject delete --name "MyCube"
```

---

### material

Material operations.

| Action | Purpose | Required Options |
|--------|---------|------------------|
| `create` | Create material | `--path` |
| `info` | Get material info | `--path` |
| `set-color` | Set material color | `--path`, `--color` |
| `set-property` | Set shader property | `--path`, `--property`, `--value` |
| `assign` | Assign to renderer | `--path`, `--target` |
| `set-renderer-color` | Set renderer color | `--target`, `--color` |

**Options:**
| Option | Description | Default |
|--------|-------------|---------|
| `--path` | Material asset path | - |
| `--shader` | Shader name | Standard |
| `--color` | r,g,b,a format (e.g., `1,0,0,1`) | - |
| `--property` | Shader property name | _BaseColor |
| `--value` | Property value | - |
| `--target` | Target GameObject name | - |
| `--slot` | Material slot index | 0 |
| `--mode` | shared, instance, or property_block | - |

```bash
umcp material info --path Assets/Materials/Default.mat
umcp material create --path Assets/Materials/New.mat --shader Standard
umcp material set-color --path Assets/Materials/New.mat --color 1,0,0,1
umcp material set-property --path Assets/Materials/Mat.mat --property _Metallic --value 0.5
umcp material assign --path Assets/Materials/New.mat --target "MyCube"
umcp material assign --path Assets/Materials/New.mat --target "MyCube" --mode instance
umcp material set-renderer-color --target "MyCube" --color 0,1,0,1
```

---

## Global Options

| Option | Description | Default |
|--------|-------------|---------|
| `--port` | MCP server port | Auto-detect (macOS) |
| `--host` | MCP server host | localhost |

---

## Troubleshooting

### Connection Refused
1. Verify Unity Editor is running
2. Check MCP for Unity plugin (Tools → MCP for Unity)
3. Try explicit port: `--port 6400`

### Timeout During Verify
1. Increase timeout: `--timeout 120 --connection-timeout 60`
2. Check Unity for blocking dialogs
3. Manual refresh in Unity (Cmd+R)

### Port Auto-Detection Failed (macOS)
1. Specify manually: `--port 6400`
2. Check Unity EditorPrefs:
   ```bash
   defaults read com.unity3d.UnityEditor5.x "MCPForUnity.UnitySocketPort"
   ```
