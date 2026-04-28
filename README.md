# R3NE — Claude Code plugin

Drive [R3NE](https://r3ne.app) (a node-based real-time VFX engine for macOS) from Claude. The plugin bundles:

- **`r3ne` MCP server** — 13 tools for creating shaders, building 3D scenes, wiring nodes, and compositions. Talks to the running R3NE.app via `http://localhost:19780`.
- **`r3ne` Skill** — teaches Claude the engine's mental model, MSL `@input` annotations, the 3D pipeline (Scene3D / Root composer), and common pitfalls. Auto-loads when you mention R3NE / VJ / shader / 3D scene.

## Prerequisites

- macOS with R3NE.app installed and running
- Node.js 18+ on `PATH` (the bundled MCP server uses it; install via `brew install node` if needed)

## Install (Claude Code)

```
/plugin marketplace add FranciscoOutonB/r3ne-mcp
/plugin install r3ne@r3ne-mcp
```

Or directly from a Git URL:

```
/plugin marketplace add https://github.com/FranciscoOutonB/r3ne-mcp.git
```

After install, restart Claude Code. Verify with:

```
> are you connected to r3ne?
```

Claude should call `mcp__r3ne__health` and respond with the running app's version.

## What you can ask

- "Clear the canvas and build a chrome torus lit by a neon spot light, slowly rotating"
- "Add a noise displace + twist modifier on the sphere, stack them in that order"
- "Write a 2D shader that does a rainbow stripes warp on the inputTex, with a `speed` slider"
- "Add a fog setting to the scene with density 0.3 and a teal color"
- "What's currently on the graph? Tell me which nodes aren't wired to Root."

## Tools exposed

| Tool | What it does |
|---|---|
| `health` | Ping the embedded API to check the app is up |
| `get_graph` | Dump the full graph — nodes, connections, params, shader source |
| `list_node_types` | All registerable types from `NodeFactory`, grouped by category |
| `list_presets` | Built-in shader templates with their MSL source |
| `clear_graph` | Remove every non-Root node |
| `create_node` | Add a node by type + optional source/params |
| `delete_node` | Remove a node + its connections |
| `connect_nodes` | Wire an output port to a typed input port |
| `disconnect` | Remove a connection by id |
| `set_params` | Update params on an existing node (incl. transforms / scene settings) |
| `create_shader` | Create a shader node, wait for compilation, return errors if any |
| `edit_shader` | Edit MSL source on an existing shader; returns compilation result |
| `get_shader_errors` | Read the compilation state for a shader node |
| `create_composition` | Atomic batch: create N nodes + M connections in one call |

## Layout

```
r3ne-mcp/
├── .claude-plugin/
│   ├── marketplace.json     # marketplace manifest (entry point for /plugin marketplace add)
│   └── plugin.json          # plugin manifest
├── .mcp.json                # registers the bundled MCP server
├── skills/r3ne/
│   ├── SKILL.md             # Claude skill — auto-loads when R3NE/shader/3D is mentioned
│   └── references/
│       ├── 3d-pipeline.md
│       └── shader-patterns.md
└── mcp-server/
    ├── package.json
    └── dist/
        └── index.mjs        # esbuild bundle (~1 MB, all deps inlined)
```

## License

MIT — see [LICENSE](LICENSE). The MCP server source is maintained privately; only the bundled output (`mcp-server/dist/index.mjs`) is distributed here.
