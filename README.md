# SimpleRenamer - KiCad Plugin

A KiCad PCB Editor plugin for renaming footprint references (RefDes) with sequential numbering.

## Demo Video

Watch the plugin in action: [YouTube Demo](https://youtu.be/WJBYaU8HHzo](https://youtu.be/hvOIygx5834)

## Features

- **Multi-selection support**: Rename multiple components at once
- **Sequential numbering**: Automatically assigns R1, R2, R3, C1, C2, etc.
- **Smart grouping**: Groups components by prefix (R, C, U, etc.) and numbers them sequentially
- **Conflict avoidance**: Automatically avoids duplicate reference designators
- **Easy to use**: Simple toolbar button integration

## Installation

### Step 1: Locate Your KiCad Plugins Directory

The location depends on your operating system and KiCad version:

#### Windows
- **KiCad 7.0**: `C:\Users\<YourUsername>\AppData\Roaming\kicad\7.0\scripting\plugins\`
- **KiCad 8.0**: `C:\Users\<YourUsername>\AppData\Roaming\kicad\8.0\scripting\plugins\`

#### Linux
- **KiCad 7.0**: `~/.local/share/kicad/7.0/scripting/plugins/`
- **KiCad 8.0**: `~/.local/share/kicad/8.0/scripting/plugins/`

#### macOS
- **KiCad 7.0**: `~/Library/Preferences/kicad/7.0/scripting/plugins/`
- **KiCad 8.0**: `~/Library/Preferences/kicad/8.0/scripting/plugins/`

### Step 2: Copy the Plugin Folder

Copy the entire `SimpleRenamer` folder (from this repository) to the plugins directory above.

**Important**: Copy only the `SimpleRenamer` folder, not the parent `KiCad-Renamer-Plugin` folder.

### Step 3: Create the Icon (if missing)

The plugin requires a `icon.png` file (32x32 pixels) in the `SimpleRenamer` folder. You can:
- Use the provided `create_icon.py` script (requires Pillow: `pip install Pillow`)
- Or create it manually with any image editor (Paint, GIMP, etc.) - draw a simple 'R' letter

### Step 4: Restart KiCad

Restart KiCad PCB Editor to load the plugin. The plugin button should appear in the toolbar.

## Usage

1. **Select components**: Select one or more footprints in your PCB (they can have different prefixes like R?, C?, U?)
2. **Run the plugin**: Click the "Simple Renamer" button in the toolbar
3. **View results**: A dialog will show all renamed components, and the console will display detailed logs

### Example

If you select:
- 3 resistors (R?, R?, R?)
- 2 capacitors (C?, C?)
- 1 IC (U?)

The plugin will rename them to:
- R1, R2, R3
- C1, C2
- U1

## Troubleshooting

### Plugin doesn't appear in toolbar
- Make sure you copied the `SimpleRenamer` folder (not the parent folder) to the correct plugins directory
- Check that `icon.png` exists in the `SimpleRenamer` folder
- Restart KiCad completely

### No components renamed
- Make sure you have selected at least one footprint before clicking the plugin button
- Check the KiCad Python console (View > Show Python Console) for error messages

### Console output
- Open the Python console in KiCad (View > Show Python Console) to see detailed rename logs

## Requirements

- KiCad 7.0 or 8.0
- Python 3.x (included with KiCad)

## License

This plugin is provided as-is for educational and personal use.
