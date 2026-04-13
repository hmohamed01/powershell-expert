# PowerShell Expert Skill

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Version](https://img.shields.io/badge/Version-1.1.0-green.svg)](CHANGELOG.md)
[![Platform](https://img.shields.io/badge/Platform-Windows%20|%20PowerShell%207+-blue.svg)](https://github.com/PowerShell/PowerShell)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-blueviolet.svg)](https://claude.ai/code)

A Claude Code skill for developing PowerShell scripts, tools, modules, and GUIs following Microsoft best practices.

## Features

- **Script Development** - Templates, naming conventions, parameter design, pipeline patterns
- **GUI Development** - Windows Forms and WPF/XAML patterns with 15+ control examples
- **PowerShell Gallery Integration** - Search, install, and manage modules via PSResourceGet
- **Module Recommendations** - Curated list of popular modules by category
- **Live Verification** - Validates module availability and cmdlet syntax against live documentation

## Installation

Copy the skill folder to your Claude Code skills directory:

```bash
cp -r powershell-expert ~/.claude/skills/
```

Or unzip the packaged skill:

```bash
unzip powershell-expert.skill -d ~/.claude/skills/
```

## Usage

The skill activates automatically when you ask Claude Code to:

- Write PowerShell scripts or functions
- Create Windows Forms or WPF GUIs
- Find or recommend PowerShell modules
- Follow PowerShell best practices

### Example Prompts

```
"Write a PowerShell script to monitor disk space"
"Create a GUI for selecting and renaming files"
"What module should I use for working with Excel files?"
"Help me add proper error handling to this script"
```

### Live Verification

When accuracy is critical, the skill verifies information against live sources:

| Verification Type | Source |
|-------------------|--------|
| Module exists/active | PowerShell Gallery |
| Cmdlet syntax | Microsoft Learn / raw GitHub docs |
| PSResourceGet cmdlets | Raw GitHub markdown (deterministic URL) |
| Version requirements | Gallery metadata |

Module recommendations are always verified before being presented. If live verification fails, the skill falls back to executing `Search-Gallery.ps1` locally or prompts the user to verify manually.

## Skill Contents

```
powershell-expert/
├── SKILL.md                 # Core workflow and quick reference
├── scripts/
│   └── Search-Gallery.ps1   # Enhanced PowerShell Gallery search
└── references/
    ├── best-practices.md    # Naming, parameters, pipeline, errors
    ├── gui-development.md   # Forms, WPF, controls, events
    └── powershellget.md     # Module management cmdlets
```

## Documentation Sources

- [PowerShell Docs](https://learn.microsoft.com/en-us/powershell/)
- [PowerShell Gallery](https://www.powershellgallery.com/)
- [Module Browser](https://learn.microsoft.com/en-us/powershell/module/)
- [PowerShell-Docs (raw)](https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/live/reference/)
- [PSResourceGet Docs (raw)](https://raw.githubusercontent.com/MicrosoftDocs/powershell-docs-psget/live/powershell-gallery/powershellget-3.x/Microsoft.PowerShell.PSResourceGet/)

## License

MIT
