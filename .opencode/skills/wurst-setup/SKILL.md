---
name: wurst-setup
description: WurstScript installation, project setup, build configuration, dependency management, and VSCode extension usage for Warcraft 3 modding
---

# WurstScript Setup and Installation

Complete guide for installing WurstScript and setting up projects.

## Prerequisites

WurstScript requires:
- VSCode (recommended) or any text editor
- Java Runtime (auto-installed with extension)
- Warcraft III (for testing)

## VSCode Installation (Recommended)

### 1. Install VSCode
Download from: https://code.visualstudio.com/

### 2. Install Wurst Extension
1. Open VSCode
2. Go to Extensions (Ctrl+Shift+X)
3. Search "Wurst"
4. Install "Wurst language support" by peterzeller

### 3. Extension Auto-Setup
The extension automatically:
- Downloads and installs the compiler
- Installs the `grill` CLI tool
- Keeps everything updated

### 4. Activate Extension
Open any `.wurst` file or run:
- `F1` -> `>wurst: Install`

## Project Creation

### Via VSCode
1. `F1` -> `>wurst: New Wurst Project`
2. Select folder location
3. Extension creates project structure

### Via CLI
```bash
grill generate my-project
```

## Project Structure

```
my-project/
├── _build/                 # Compiled output (generated)
├── .vscode/
│   └── settings.json       # VSCode workspace settings
├── imports/                # Files to import into map
├── wurst/                  # Wurst source files
│   └── Hello.wurst         # Example package
├── ExampleMap.w3x          # Base map file
├── wurst.build             # Project configuration
├── wurst.dependencies      # Generated dependency list
├── wurst_run.args          # Run configuration
└── .gitignore
```

## wurst.build Configuration

```yaml
projectName: MyProject
dependencies:
  - https://github.com/wurstscript/WurstStdlib2
buildMapData:
  name: My Map
  author: Your Name
  fileName: MyMap
  scenarioData:
    description: Map description
    suggestedPlayers: 1-8
    loadingScreen:
      model: ""
      title: My Map
      subtitle: ""
      text: Loading...
```

### Configuration Options

```yaml
projectName: string         # Project name
dependencies: []            # Git URLs of dependencies
buildMapData:
  name: string              # Map name shown in-game
  author: string            # Author name
  fileName: string          # Output file name (no extension)
  scenarioData:
    description: string
    suggestedPlayers: string
    loadingScreen:
      model: string         # Loading screen model path
      title: string
      subtitle: string
      text: string
```

## Managing Dependencies

### Add Dependency
```bash
grill install https://github.com/some/library
```

This updates `wurst.build` and downloads the library.

### Update Dependencies
```bash
grill install
```

### Common Dependencies
```yaml
dependencies:
  # Standard Library (required)
  - https://github.com/wurstscript/WurstStdlib2
  
  # Popular Community Libraries
  - https://github.com/Frotty/Frentity
  - https://github.com/theQuazz/wurst-lodash
  - https://github.com/Frotty/wurst-item-shop
```

## Running and Building

### Run Map (Development)
- `F1` -> `>wurst: Run map`
- Or use hotkey if configured

This:
1. Compiles your code
2. Creates `WurstRunMap.w3x` in WC3 maps folder
3. Launches Warcraft III with the map

### Build Map (Release)
- `F1` -> `>wurst: Build map`

Creates compiled map in `_build/` folder for distribution.

### wurst_run.args

Configure run behavior:

```
-runmapaliases ["wc3path:C:\Games\Warcraft III\_retail_\x86_64\Warcraft III.exe"]
-preloadFiles []
```

## CLI Reference (grill)

```bash
# Create new project
grill generate <project-name>

# Install/update dependencies
grill install

# Add specific dependency
grill install <git-url>

# Update compiler
grill install wurstscript

# Build map
grill build <mapfile.w3x>

# Run map
grill run <mapfile.w3x>
```

## Adding to PATH

Add `~/.wurst` to your PATH for global `grill` access:

### Windows
1. Open Environment Variables
2. Edit PATH in user variables
3. Add `%USERPROFILE%\.wurst`

### macOS/Linux
Add to `~/.bashrc` or `~/.zshrc`:
```bash
export PATH="$PATH:$HOME/.wurst"
```

## Working with Maps

### Base Map
`ExampleMap.w3x` (or your map) is the "terrain" map:
- Edit terrain, doodads, destructibles in World Editor
- Wurst reads this and adds compiled code
- Wurst never modifies this file

### Imports Folder
Files in `imports/` are automatically imported:
```
imports/
├── Models/
│   └── MyModel.mdx
├── Icons/
│   └── MyIcon.blp
└── war3map.j              # Not recommended
```

Folder structure is preserved in the map.

### Updating Base Map
After adding imports via wurst:
1. Build the map
2. Copy from `_build/` to project root
3. Open in World Editor
4. Save (clears wurst code but keeps imports)
5. Now use as new base map

## Troubleshooting

### Extension Not Loading
1. Open a `.wurst` file
2. Check Output panel -> Wurst
3. Run `F1` -> `>wurst: Install`

### Compile Errors
- Check Problems panel (Ctrl+Shift+M)
- Hover over red underlines for details
- Check import statements

### Map Won't Run
- Verify WC3 path in `wurst_run.args`
- Check if WC3 is already running
- Try running WC3 manually first

### Dependency Issues
```bash
# Clean and reinstall
rm -rf _build/dependencies
grill install
```

### Java Issues
The extension manages Java automatically. If issues occur:
- Check Output panel for errors
- Reinstall extension
- Manually install Java 11+

## VSCode Tips

### Useful Shortcuts
- `F1` - Command palette
- `Ctrl+.` - Quick actions
- `Ctrl+Space` - Autocomplete
- `Ctrl+Click` - Go to definition
- `Shift+F12` - Find references

### Recommended Settings
`.vscode/settings.json`:
```json
{
  "editor.formatOnSave": true,
  "wurst.wc3InstallationPath": "C:\\Games\\Warcraft III"
}
```

## Resources

- Documentation: https://wurstlang.org/start.html
- VSCode Extension: https://marketplace.visualstudio.com/items?itemName=peterzeller.wurst
- Discord Support: https://discord.gg/mSHZpWcadz
