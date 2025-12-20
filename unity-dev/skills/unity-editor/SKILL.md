---
name: unity-editor
description: Comprehensive Unity Editor operations using unity-mcp-client CLI via uvx. Covers daily workflows (refresh, console, build verification), asset management, scene/object manipulation, and debugging. This skill should be used when users request Unity Editor operations, error analysis, log review, asset refresh, test execution, or after making script/asset modifications.
---

# Unity Editor Operations

## Overview

Streamline Unity Editor operations through `unity-mcp-client` CLI via uvx. Handle daily development workflows, asset management, debugging, and scene operations with automated verification cycles.

## CLI Setup

All Unity Editor operations use `unity-mcp-client` via `uvx`:

```bash
# Base command (port auto-detected on macOS)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp <command>

# With explicit port
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp --port 6400 <command>
```

**Note:** On macOS, port is auto-detected from Unity EditorPrefs. Manual `--port` is only needed for non-standard setups.

### Available Commands

| Command | Purpose | Options |
|---------|---------|---------|
| `verify` | Full validation (refresh→clear→wait→console) | `--timeout`, `--retry`, `--types` |
| `refresh` | Refresh asset database | None |
| `state` | Get editor state (isCompiling, isPlaying) | None |
| `console` | Get console logs | `--types`, `--count` |
| `clear` | Clear console | None |
| `play` | Enter Play Mode | None |
| `stop` | Exit Play Mode | None |
| `find` | Find GameObject | `<name>` |
| `tests` | Run tests | `<mode>` (edit/play) |
| `scene` | Scene operations | `<action>` (see below) |

### Global Options

| Option | Description | Default |
|--------|-------------|---------|
| `--port` | MCP server port | Auto-detect (macOS) |
| `--host` | MCP server host | localhost |
| `--types` | Log types (error, warning, log) | error warning |
| `--count` | Number of logs to retrieve | 20 |
| `--timeout` | Max wait for compilation (verify only) | 60s |
| `--retry` | Max connection retry attempts (verify only) | 3 |
| `--name` | Scene name (scene create/load) | None |
| `--path` | Scene path (scene create/load/save) | None |
| `--build-index` | Build index (scene load) | None |

### Scene Actions

| Action | Purpose | Required Options |
|--------|---------|------------------|
| `active` | Get active scene info | None |
| `hierarchy` | Get scene hierarchy tree | None |
| `build-settings` | Get scenes in build settings | None |
| `load` | Load scene | `--name`, `--path`, or `--build-index` |
| `save` | Save current scene | `--name`, `--path` (optional) |
| `create` | Create new scene | `--name` (required), `--path` (optional) |

Refer to `references/mcp-commands.md` for detailed documentation.

## Core Workflows

### 1. Build Verification (Post-Edit)

**Trigger:** After editing C# scripts, shaders, or .asmdef files

**Workflow:**
```
Edit Script → Refresh Assets → Wait for Compile → Check Console → Fix if Errors
     ↑                                                              |
     └──────────────────── Repeat ←────────────────────────────────┘
```

**Single Command:**
```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify
```

This command executes:
1. Asset refresh
2. Console clear
3. Compilation wait (polling `isCompiling`)
4. Console log check

**With Custom Options:**
```bash
# Extended timeout for large projects
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify --timeout 120

# Include all log types
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify --types error warning log

# More retry attempts for unstable connections
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify --retry 5
```

**Critical:** Never skip verification after script changes. Always verify before reporting task completion.

### 2. Console Log Analysis

**Trigger:** User requests "check logs", "analyze errors", or debugging assistance

**Log Types:**
- `error` - Compilation and runtime errors (critical)
- `warning` - Deprecation, performance warnings (medium)
- `log` - Debug.Log output (informational)

**Commands:**
```bash
# Get errors only
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console --types error

# Get errors and warnings (default)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console

# Get all logs with limit
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp console --types error warning log --count 50
```

**Workflow:**
1. Retrieve console logs with appropriate filter
2. Parse error codes and locations (e.g., `CS0246 at Scripts/Player.cs:15`)
3. Reference `references/error-patterns.md` for known patterns
4. Report findings with prioritization
5. Suggest fixes with file paths and line numbers

### 3. Test Execution

**Trigger:** User requests test run, or before commit/PR

**Prerequisites:**
- Successful compilation (run `verify` first)

**Commands:**
```bash
# Run Edit Mode tests
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp tests edit

# Run Play Mode tests
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp tests play
```

**Workflow:**
1. Run `verify` first to ensure clean compilation
2. Execute tests
3. Analyze results
4. Report failures with stack traces

### 4. Asset Management

**Trigger:** User mentions prefabs, ScriptableObjects, or asset operations

**Commands:**
```bash
# Full verification (recommended)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp verify

# Refresh only (without waiting)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp refresh
```

**Best Practices:**
- Use `verify` after external file changes
- Validate prefab connections before build
- Check for null references in ScriptableObjects

### 5. Scene & GameObject Operations

**Trigger:** User requests scene management or object manipulation

**Scene Operations:**
```bash
# Get active scene info
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene active

# Get scene hierarchy
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene hierarchy

# Get build settings (scenes in build)
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene build-settings

# Load scene by name
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene load --name MainScene

# Load scene by path
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene load --path Assets/Scenes/Level1.unity

# Load scene by build index
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene load --build-index 0

# Save current scene
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene save

# Create new scene
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp scene create --name NewScene --path Assets/Scenes
```

**Find GameObject:**
```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp find "PlayerController"
```

**Play Mode Control:**
```bash
# Enter Play Mode
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp play

# Exit Play Mode
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp stop
```

**Editor State:**
```bash
uvx --from git+https://github.com/bigdra50/unity-mcp-client unity-mcp state
```

## Error Resolution Strategy

### Classification

| Error Type | Identification | Priority |
|-----------|----------------|----------|
| Compilation (CS####) | CS error codes | Critical - fix immediately |
| Assembly Reference | Missing .asmdef refs | Critical |
| Runtime Exception | NullReference, Missing | High - fix when requested |
| Deprecation Warning | Obsolete API usage | Medium |
| Performance Warning | GC allocation, etc. | Low |

### Resolution Process

1. **Understand**: Parse error message, identify file/line/type
2. **Classify**: Determine if compilation or runtime
3. **Identify Root Cause**: Check related code, dependencies
4. **Apply Fix**: Targeted change to specific location
5. **Verify**: Run `verify` command to confirm resolution
6. **Iterate**: Continue until all errors resolved

### Iteration Limits

- Track fix attempts to avoid infinite loops
- After 3 failed attempts, ask user for guidance
- Consider alternative approaches when primary fix fails

## State Response Format

The `state` command returns JSON:
```json
{
  "success": true,
  "data": {
    "isPlaying": false,
    "isPaused": false,
    "isCompiling": false,
    "activeSceneName": "MainGame"
  }
}
```

## Best Practices

### Proactive Verification
- Run `verify` after making changes
- Check logs before reporting task completion
- Ensure clean compilation state

### Error Communication
- Report errors with file paths and line numbers (e.g., `Scripts/Player.cs:42`)
- Explain error cause in user-friendly terms
- Provide specific, actionable fixes

### Testing Integration
- Run tests only after successful compilation
- Use `verify` before running tests
- Check Enter Play Mode settings for Play Mode tests

## Troubleshooting

### Connection Failures

If CLI commands fail:
1. Verify Unity Editor is running
2. Check MCP for Unity settings (Tools → MCP for Unity → Settings)
3. Use `--retry` option for transient failures
4. Manually specify `--port` if auto-detection fails

### Timeout Issues

If compilation takes too long:
1. Increase timeout: `--timeout 120`
2. Check Unity Editor for blocking dialogs
3. Try manual refresh in Unity (Cmd+R)

### Port Detection Issues (macOS)

If auto-detection fails:
- Manually specify: `--port 6400`
- Check Unity EditorPrefs
- Verify MCP for Unity plugin installation

## Usage Triggers

Use this skill when:
- User reports Unity errors or issues
- User requests error analysis or debugging
- User asks to "check logs" or "analyze console"
- After making script or asset modifications
- Before running Unity tests
- User mentions compilation problems
- User describes runtime exceptions
- User requests asset refresh or management
- User asks about scene or GameObject operations
- Setting up error-free Unity project state
