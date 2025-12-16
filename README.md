# Path Sketcher

A visual editor for creating and editing Bézier paths for 2D games, built with Zig and raylib.

## Overview

Path Sketcher is a desktop tool for creating smooth, scalable paths that can be used in arcade-style games for enemy movement patterns, player trajectories, or any other path-based animation. It uses Bézier curves with anchor points and handles for precise control over path shape.

## Features

- **Visual Path Editor**
  - Click to add anchor points
  - Right-click to remove points
  - Drag anchor points to reposition
  - Drag handles to adjust curve shape
  - Real-time Bézier curve preview

- **Anchor Point Modes**
  - **Corner**: Independent control handles (sharp corners)
  - **Smooth**: Mirrored handles for smooth curves
  - **Aligned**: Aligned but independently scaled handles
  - Press `M` or use toolbar button to cycle modes

- **Path Management**
  - Create, rename, duplicate, and delete paths
  - Auto-save to disk
  - Quick reload from files
  - Sidebar list of all available paths

- **Transform Tools**
  - Flip paths horizontally or vertically
  - Normalized coordinates (0-1 range)
  - Preserves handle relationships

- **DPI Awareness**
  - Automatic high-DPI display support
  - Scales UI elements for crisp rendering
  - Maintains consistent sizing across monitors

## Requirements

- Zig 0.15.2 or later
- No external dependencies (raylib is bundled)

## Installation

### From Source
```bash
# Clone the repository
git clone https://github.com/codemonkey76/path-sketcher.git
cd path-sketcher

# Build and run
zig build run
```

### Building Release Version
```bash
# Build optimized binary
zig build -Doptimize=ReleaseFast

# Binary will be in zig-out/bin/
```

## Usage

### First Launch

On first launch, Path Sketcher will create a configuration file at:
- **Linux/macOS**: `~/.local/share/Sketch/config.zon`
- **Windows**: `%APPDATA%\Sketch\config.zon`

The config file contains the default path to store your path files. You can edit this to point to your game's asset directory.

### Creating Paths

1. **Click "New"** to create a new path
2. **Click in the canvas** to add anchor points
3. **Drag anchor points** to position them
4. **Drag handles** (red/green circles) to adjust curve shape
5. **Right-click** on anchor points to delete them
6. **Click "Save"** to save your path

### Editing Paths

- Select a path from the sidebar list
- If you have unsaved changes, you'll be prompted before switching
- Use keyboard navigation (↑/↓/Home/End) in the path list

### Keyboard Shortcuts

- **M**: Cycle anchor point mode (Corner → Smooth → Aligned)
- **F11**: Toggle fullscreen
- **Q**: Quit application
- **↑/↓**: Navigate path list
- **Home/End**: Jump to first/last path
- **Escape**: Cancel modal dialogs
- **Enter**: Confirm modal dialogs

### Path File Format

Paths are saved as `.zon` files (Zig Object Notation) in your configured asset directory:
```zig
.{
    .anchor_points = .{
        .{
            .pos = .{ .x = 0.5, .y = 0.1 },
            .handle_in = null,
            .handle_out = .{ .x = 0.0, .y = 0.2 },
            .mode = .smooth,
        },
        // ... more anchor points
    },
}
```

## Integration with Games

Path Sketcher is designed to work with the [arcade-lib](https://github.com/codemonkey76/arcade-lib) Zig library for loading and using paths in games.

### Example Usage in a Game
```zig
const arcade = @import("arcade_lib");

// Load paths
var paths = try arcade.PathRegistry.init(allocator, "assets/paths");
try paths.load();

// Get a specific path
const enemy_path = paths.getPath("enemy_wave_1") orelse return;

// Create a path definition
const path_def = arcade.PathDefinition{
    .control_points = try arcade.PathDefinition.fromAnchorPoints(
        allocator,
        enemy_path,
    ),
};

// Use the path for movement
const t = time / duration; // 0.0 to 1.0
const pos = path_def.getPosition(t);
enemy.x = pos.x * game_width;
enemy.y = pos.y * game_height;
```

## Architecture

### Project Structure
```
path-sketcher/
├── src/
│   ├── main.zig              # Application entry point
│   ├── root.zig              # Module exports
│   ├── config.zig            # Configuration management
│   ├── models/
│   │   ├── app.zig           # Main application model
│   │   ├── path_editor.zig   # Path editing logic
│   │   └── path_list.zig     # Path list management
│   └── ui/                   # UI components
│       ├── ui.zig            # Core UI state
│       ├── canvas.zig        # Path canvas editor
│       ├── listbox.zig       # Path list widget
│       ├── button.zig        # Button widget
│       ├── text_input.zig    # Text input widget
│       ├── toolbar.zig       # Toolbar widget
│       └── modals/           # Modal dialogs
├── assets/
│   └── fonts/                # Embedded fonts
└── build.zig
```

### Key Components

- **Canvas Editor**: Handles path visualization and editing
- **Path Registry**: Manages loading/saving paths from disk
- **Immediate Mode UI**: Simple, efficient UI rendering
- **Modal System**: Clean state management for dialogs

## Configuration

Edit your config file to customize:
```zig
.{
  .app_name = "Sketch",
  .version = "1.0.0",
  .asset_path = "/path/to/your/game/assets/paths",
  .debug = false,
}
```

## Dependencies

Path Sketcher uses:
- [raylib-zig](https://github.com/Not-Nik/raylib-zig) - Zig bindings for raylib
- [arcade-lib](https://github.com/codemonkey76/arcade-lib) - Path definition library

Both are managed via Zig's package manager.

## Building for Distribution

### Windows
```bash
zig build -Doptimize=ReleaseSmall -Dtarget=x86_64-windows
```

The executable will be standalone (raylib is statically linked).

### Linux
```bash
zig build -Doptimize=ReleaseSmall -Dtarget=x86_64-linux
```

### macOS
```bash
zig build -Doptimize=ReleaseSmall -Dtarget=x86_64-macos
```

## Contributing

Contributions are welcome! Please open an issue or PR.

### Development Setup

1. Install Zig 0.15.2+
2. Clone the repository
3. Run `zig build run` to start developing
4. Use `zig build test` to run tests

## Roadmap

- [ ] Copy/paste anchor points
- [ ] Undo/redo support
- [ ] Path preview animation
- [ ] Export to other formats (JSON, SVG)
- [ ] Grid snapping
- [ ] Path templates
- [ ] Batch operations on multiple paths
- [ ] Bezier path simplification

## License

MIT License - see [LICENSE](LICENSE) for details.

## Acknowledgments

- Built with [Zig](https://ziglang.org/)
- Uses [raylib](https://www.raylib.com/) via [raylib-zig](https://github.com/Not-Nik/raylib-zig)
- Font: [Inter](https://rsms.me/inter/)

## Related Projects

- [arcade-lib](https://github.com/codemonkey76/arcade-lib) - Arcade game utilities
- [zalaga](https://github.com/codemonkey76/zalaga) - Galaga clone using these paths
- [zig-arcade-engine](https://github.com/codemonkey76/zig-arcade-engine) - 2D game engine

## Support

For questions or issues, please [open an issue](https://github.com/codemonkey76/path-sketcher/issues) on GitHub.
