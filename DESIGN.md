# SimpleRenamer Plugin - Architecture Documentation

## Overview

SimpleRenamer is a KiCad PCB Editor plugin that provides automated renaming of footprint references (RefDes) with sequential numbering. It extends KiCad's functionality through the `pcbnew.ActionPlugin` API.

## Architecture

### Plugin Structure

```
SimpleRenamer/
├── __init__.py         # Plugin registration entry point
├── simple_plugin.py    # Main plugin logic
└── icon.png            # Toolbar icon (32x32 PNG)
```

### Class Hierarchy

```
pcbnew.ActionPlugin
    └── SimpleRenamerPlugin
```

### Core Components

#### 1. Plugin Registration (`__init__.py`)

- **Purpose**: Entry point for KiCad plugin discovery
- **Function**: Instantiates and registers the plugin with KiCad
- **Key Method**: `SimpleRenamerPlugin().register()`

#### 2. Main Plugin Class (`SimpleRenamerPlugin`)

**Inheritance**: `pcbnew.ActionPlugin`

**Key Attributes**:
- `name`: Plugin display name ("Simple Renamer")
- `category`: Plugin category ("Modify PCB")
- `description`: Plugin description
- `show_toolbar_button`: Enable toolbar button (True)
- `icon_file_name`: Icon file name ("icon.png")

**Key Methods**:

1. **`__init__(self)`**
   - Initializes plugin metadata
   - Configures toolbar button and icon

2. **`defaults(self)`**
   - Called when plugin is loaded
   - Currently a placeholder for future initialization logic

3. **`extract_prefix(self, reference)`**
   - **Purpose**: Extract component prefix from reference designator
   - **Input**: Reference string (e.g., "R?", "C5", "U?")
   - **Output**: Prefix string (e.g., "R", "C", "U")
   - **Algorithm**: Uses regex to match leading alphabetic characters
   - **Fallback**: Returns original reference if no prefix found

4. **`Run(self)`**
   - **Purpose**: Main execution entry point (called when button clicked)
   - **Workflow**:
     1. Get current board instance
     2. Collect all selected footprints
     3. Group footprints by prefix
     4. Rename sequentially within each group
     5. Avoid reference conflicts
     6. Refresh display and show summary

## Algorithm Details

### Selection Collection

```python
for footprint in board.GetFootprints():
    if footprint.IsSelected():
        selected_footprints.append(footprint)
```

- Iterates through all footprints on the board
- Filters for selected footprints only
- Stores in list for processing

### Prefix Extraction

```python
match = re.match(r'^([A-Za-z]+)', reference)
```

- Uses regex pattern to match one or more alphabetic characters at start
- Captures prefix (R, C, U, etc.)
- Handles references with or without numbers

### Grouping Logic

```python
grouped_footprints = defaultdict(list)
for fp in selected_footprints:
    prefix = extract_prefix(fp.GetReference())
    grouped_footprints[prefix].append(fp)
```

- Groups footprints by their prefix
- Uses `defaultdict` for automatic list creation
- Preserves order within groups

### Sequential Renaming

```python
for prefix, fp_list in grouped_footprints.items():
    for index, fp in enumerate(fp_list, start=1):
        new_ref = f"{prefix}{index}"
```

- Processes each prefix group separately
- Numbers sequentially starting from 1
- Format: `{prefix}{number}` (e.g., R1, R2, C1)

### Conflict Avoidance

```python
existing_refs = set()  # Collect all existing references
while new_ref in existing_refs:
    counter += 1
    new_ref = f"{prefix}{counter}"
```

- Maintains set of all existing references on board
- Checks for conflicts before assigning
- Increments counter until unique reference found
- Updates set with new references to prevent intra-batch conflicts

## Data Flow

```
User Action (Click Button)
    ↓
Run() Method Called
    ↓
Get Board Instance
    ↓
Collect Selected Footprints
    ↓
Group by Prefix
    ↓
For Each Group:
    ├─ Extract Prefix
    ├─ Check Existing References
    ├─ Assign Sequential Number
    ├─ Avoid Conflicts
    └─ Update Footprint Reference
    ↓
Refresh Display
    ↓
Show Summary Dialog
```

## API Dependencies

### KiCad API (`pcbnew`)

- `pcbnew.ActionPlugin`: Base class for action plugins
- `pcbnew.GetBoard()`: Get current board instance
- `board.GetFootprints()`: Get all footprints on board
- `footprint.IsSelected()`: Check if footprint is selected
- `footprint.GetReference()`: Get current reference designator
- `footprint.SetReference()`: Set new reference designator
- `pcbnew.Refresh()`: Refresh PCB display

### wxPython API (`wx`)

- `wx.MessageBox()`: Display dialog messages

### Python Standard Library

- `re`: Regular expressions for prefix extraction
- `collections.defaultdict`: Grouping data structure

## Error Handling

1. **No Board Loaded**
   - Checks if board exists
   - Shows error message if not

2. **No Selection**
   - Checks if any footprints are selected
   - Shows informational message
   - Returns early

3. **Reference Conflicts**
   - Automatically resolves by finding next available number
   - No user intervention required

## Extension Points

### Future Enhancements

1. **Position-based Sorting**: Sort components by X/Y coordinates before numbering
2. **Custom Numbering Schemes**: Allow user to specify starting number or pattern
3. **Undo Support**: Track changes for undo functionality
4. **Batch Processing**: Process all unnumbered components automatically
5. **Configuration Dialog**: Settings for numbering preferences

### Potential Improvements

- Add progress bar for large selections
- Support for hierarchical numbering (R1.1, R1.2)
- Integration with schematic for cross-reference updates
- Export rename log to file

## Testing Considerations

1. **Single Selection**: Verify single component renaming
2. **Multiple Selection**: Verify multiple components with same prefix
3. **Mixed Prefixes**: Verify components with different prefixes
4. **Conflict Resolution**: Verify handling of existing references
5. **Edge Cases**: Empty selection, no board, invalid references

## Performance Notes

- Time complexity: O(n*m) where n = selected footprints, m = total footprints (for conflict checking)
- Space complexity: O(n + m) for storing references
- Optimizations possible: Cache existing references, batch updates

## Security Considerations

- No external file access
- No network operations
- Only modifies board references (no file system changes)
- Uses KiCad's built-in API (sandboxed)
