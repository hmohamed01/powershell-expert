---
name: powershell-expert
description: Develop PowerShell scripts, tools, modules, and GUIs following Microsoft best practices. Use when writing PowerShell code, creating Windows Forms/WPF interfaces, working with PowerShell Gallery modules, or needing cmdlet/module recommendations. Covers script development, parameter design, pipeline handling, error management, and GUI creation patterns. Verifies module availability and cmdlet syntax against live documentation when accuracy is critical.
---

# PowerShell Expert

Develop production-quality PowerShell scripts, tools, and GUIs using Microsoft best practices and the PowerShell ecosystem.

## Quick Reference

### Script Structure
```powershell
#Requires -Version 5.1

<#
.SYNOPSIS
    Brief description.
.DESCRIPTION
    Detailed description.
.PARAMETER Name
    Parameter description.
.EXAMPLE
    Example-Usage -Name 'Value'
#>

[CmdletBinding()]
param(
    [Parameter(Mandatory, ValueFromPipeline)]
    [ValidateNotNullOrEmpty()]
    [string[]]$Name,

    [switch]$Force
)

begin {
    # One-time setup
}

process {
    foreach ($item in $Name) {
        # Per-item processing
    }
}

end {
    # Cleanup
}
```

### Function Template
```powershell
function Verb-Noun {
    [CmdletBinding(SupportsShouldProcess)]
    param(
        [Parameter(Mandatory, Position = 0)]
        [string]$Name,

        [Parameter(ValueFromPipelineByPropertyName)]
        [Alias('CN')]
        [string]$ComputerName = $env:COMPUTERNAME,

        [switch]$PassThru
    )

    process {
        if ($PSCmdlet.ShouldProcess($Name, 'Action')) {
            # Implementation
            if ($PassThru) { Write-Output $result }
        }
    }
}
```

## Workflow

### 1. Script Development
Follow naming and parameter conventions:
- **Verb-Noun** format with approved verbs (`Get-Verb`)
- **Strong typing** with validation attributes
- **Pipeline support** via `ValueFromPipeline`
- **-WhatIf/-Confirm** for destructive operations

See [best-practices.md](references/best-practices.md) for complete guidelines.

### 2. GUI Development
Windows Forms for simple dialogs, WPF/XAML for complex interfaces:

```powershell
Add-Type -AssemblyName System.Windows.Forms
Add-Type -AssemblyName System.Drawing

$form = New-Object System.Windows.Forms.Form -Property @{
    Text          = 'Title'
    Size          = New-Object System.Drawing.Size(400, 300)
    StartPosition = 'CenterScreen'
}
```

See [gui-development.md](references/gui-development.md) for controls, events, and templates.

### 3. PowerShell Gallery Integration
Search and install modules using PSResourceGet:

```powershell
# Search gallery
Find-PSResource -Name 'ModuleName' -Repository PSGallery

# Install module
Install-PSResource -Name 'ModuleName' -Scope CurrentUser -TrustRepository
```

Use [scripts/Search-Gallery.ps1](scripts/Search-Gallery.ps1) for enhanced search.

See [powershellget.md](references/powershellget.md) for full cmdlet reference.

## Key Patterns

### Error Handling
```powershell
try {
    $result = Get-Content -Path $Path -ErrorAction Stop
}
catch [System.IO.FileNotFoundException] {
    Write-Error "File not found: $Path"
    return
}
catch {
    throw
}
```

### Splatting for Readability
```powershell
$params = @{
    Path        = $sourcePath
    Destination = $destPath
    Recurse     = $true
    Force       = $true
}
Copy-Item @params
```

### Pipeline Best Practices
```powershell
# Stream output immediately
foreach ($item in $collection) {
    Process-Item $item | Write-Output
}

# Accept pipeline input
param(
    [Parameter(ValueFromPipeline)]
    [string[]]$InputObject
)
process {
    foreach ($obj in $InputObject) {
        # Process each
    }
}
```

## Module Recommendations

When recommending modules, search the PowerShell Gallery. These are common starting points — **always verify via [Module Verification](#module-verification) before recommending**:

| Category | Popular Modules |
|----------|----------------|
| **Azure** | `Az`, `Az.Compute`, `Az.Storage` |
| **Testing** | `Pester`, `PSScriptAnalyzer` |
| **Console** | `PSReadLine`, `Terminal-Icons` |
| **Secrets** | `Microsoft.PowerShell.SecretManagement` |
| **Web** | `Pode` (web server), `PoshRSJob` (async) |
| **GUI** | `WPFBot3000`, `PSGUI` |

## Documentation Lookup

Verify cmdlet syntax and module availability against live sources rather than recalled
detail. Every `learn.microsoft.com` PowerShell page is generated from markdown in a
public MicrosoftDocs repository — **fetch the markdown, not the rendered page**. The
source is far smaller, carries no navigation chrome or JavaScript, and preserves syntax
blocks and parameter tables verbatim.

### Raw Markdown Sources (WebFetch)

Use the **WebFetch** tool against `raw.githubusercontent.com`. Construct the URL
directly from the templates below — do not search for it first.

| Command kind | URL template |
|--------------|--------------|
| **Core PowerShell**<br>`Get-ChildItem`, `Write-Output`, `Invoke-Command` | `https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/main/reference/{version}/{Module}/{Cmdlet}.md` |
| **Conceptual**<br>`about_Splatting`, `about_CommonParameters` | `https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/main/reference/{version}/Microsoft.PowerShell.Core/About/{about_Topic}.md` |
| **Windows-only**<br>`Get-ADUser`, `Get-DnsServerZone`, `Get-MpComputerStatus` | `https://raw.githubusercontent.com/MicrosoftDocs/windows-powershell-docs/main/docset/winserver2025-ps/{Module}/{Cmdlet}.md` |
| **Gallery / packaging**<br>`Install-PSResource`, `Find-PSResource` | `https://raw.githubusercontent.com/MicrosoftDocs/powershell-docs-psget/main/powershell-gallery/powershellget-3.x/Microsoft.PowerShell.PSResourceGet/{Cmdlet}.md` |
| **Satellite modules**<br>`Invoke-ScriptAnalyzer`, `Get-Secret` | `https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs-Modules/main/reference/ps-modules/{Module}/{Cmdlet}.md` |

`{version}` is one of `5.1`, `7.4`, `7.5`, `7.6`, `7.7`. Pin it from context —
`#Requires`, `pwsh`, ISE, or a stated platform. **With no signal, fetch both `7.5` and
`5.1` and state any parameter difference**, since the wrong edition yields syntax that
looks authentic but fails on the user's shell.

**WebFetch prompt**: `Extract the complete syntax blocks, the full parameter table with
types and defaults, and the examples.`

Three rules govern URL construction:

1. **Case matters.** Raw paths are case-sensitive where `learn.microsoft.com` is not.
   `.../activedirectory/Get-ADUser.md` returns 404; `.../ActiveDirectory/Get-ADUser.md`
   returns 200. Preserve the module's own casing.
2. **Never point at a folder.** Raw serves blobs only — any URL ending in `/` returns
   404. There is no directory-listing endpoint.
3. **Convert page URLs.** Rewrite `github.com/{owner}/{repo}/blob/{branch}/{path}` to
   `raw.githubusercontent.com/{owner}/{repo}/{branch}/{path}`.

Routing a cmdlet to the right repository, module, and version folder is covered in
**[doc-sources.md](references/doc-sources.md)**, along with complete module inventories
and a 404 troubleshooting table. Consult it whenever the correct path is not obvious.

### Fallback Chain

Escalate only on failure, one step at a time:

| Step | Tool | Use when |
|------|------|----------|
| 1 | **WebFetch** raw markdown | Default — always attempt first |
| 2 | **WebFetch** contents API<br>`https://api.github.com/repos/{owner}/{repo}/contents/{path}` | A folder name is unknown. Returns plain JSON that parses cleanly; 60 requests/hour unauthenticated |
| 3 | **WebFetch** `learn.microsoft.com` | No raw source exists (third-party modules, Learn-only articles) |
| 4 | **WebSearch** | Path cannot be derived. Query: `{Cmdlet-Name} site:learn.microsoft.com/en-us/powershell` — then convert the result back to a raw URL and return to step 1 |
| 5 | Local execution | Tools unavailable. Ask the user to run `Get-Help {Cmdlet} -Full` or `Get-Command {Cmdlet} -Syntax` |

If every step fails, state the uncertainty explicitly rather than guessing:

> "I wasn't able to verify this against live documentation. Please confirm by running:
> `Get-Command Cmdlet-Name -Syntax`"

### Module Verification

Gallery packages have no markdown source. **WebFetch** the listing page directly:

- **URL**: `https://www.powershellgallery.com/packages/{ModuleName}`
- **Prompt**: `Extract: module name, latest version, last updated date, total downloads,
  and whether it shows any deprecation warning or 'unlisted' status`

A 404 means the module likely does not exist — confirm with **WebSearch** for
`{ModuleName} PowerShell module site:powershellgallery.com`. If both tools are
unavailable, execute `scripts/Search-Gallery.ps1 -Name 'ModuleName'`.

| Scenario | Requirement |
|----------|-------------|
| User asks whether module X exists | **MUST** verify on the Gallery |
| Recommending a specific module | **MUST** verify it exists and is not deprecated |
| Stating a module version requirement | **MUST** check the Gallery for the current version |
| Providing exact cmdlet syntax | **SHOULD** verify against raw markdown |
| General best practices | Static references suffice |

**Good** (verified against live data):
> "ImportExcel (v7.8.10, updated Oct 2024, 17M+ downloads) provides `Export-Excel` for
> creating spreadsheets without Excel installed."

**Bad** (unverified):
> "Use the Excel-Tools module to export data." ← may not exist

### Rendered Documentation

Browse these manually; prefer the raw sources above when fetching.

| Resource | URL |
|----------|-----|
| PowerShell documentation | https://learn.microsoft.com/en-us/powershell/ |
| Module browser | https://learn.microsoft.com/en-us/powershell/module/ |
| PowerShell Gallery | https://www.powershellgallery.com |

## Reference Files

| Topic | File |
|-------|------|
| Documentation routing, raw URL maps, 404 troubleshooting | [references/doc-sources.md](references/doc-sources.md) |
| Naming, parameters, pipeline, error handling, code style | [references/best-practices.md](references/best-practices.md) |
| Windows Forms, WPF, controls, events, templates | [references/gui-development.md](references/gui-development.md) |
| Find, install, update, publish modules | [references/powershellget.md](references/powershellget.md) |

## Included Scripts

| Script | Purpose |
|--------|---------|
| [scripts/Search-Gallery.ps1](scripts/Search-Gallery.ps1) | Formatted PowerShell Gallery search with legacy `Find-Module` fallback |
