# PowerShell Documentation Sources

Routing map for fetching authoritative cmdlet documentation as raw markdown.

Every `learn.microsoft.com` page in the PowerShell docset is generated from a markdown
file in a public MicrosoftDocs repository. Fetch the markdown instead of the rendered
page: the source is a fraction of the payload, carries no navigation chrome or
JavaScript, and preserves the syntax blocks and parameter tables verbatim.

---

## Source Selection

Route by what kind of command is being documented:

| Command belongs to | Repository | Section |
|--------------------|------------|---------|
| PowerShell engine (`Get-ChildItem`, `Write-Output`, `Invoke-Command`) | `MicrosoftDocs/PowerShell-Docs` | [Core PowerShell](#1-core-powershell) |
| `about_*` conceptual topic | `MicrosoftDocs/PowerShell-Docs` | [Conceptual Topics](#conceptual-topics) |
| Windows-only feature (`Get-ADUser`, `Get-DnsServerZone`, `Get-MpComputerStatus`) | `MicrosoftDocs/windows-powershell-docs` | [Windows PowerShell](#2-windows-powershell-modules) |
| Gallery / package management (`Install-PSResource`, `Find-PSResource`) | `MicrosoftDocs/powershell-docs-psget` | [PSResourceGet](#3-psresourceget-and-powershellget) |
| First-party satellite module (`Invoke-ScriptAnalyzer`, `Get-Secret`) | `MicrosoftDocs/PowerShell-Docs-Modules` | [Satellite Modules](#4-satellite-modules) |
| Third-party Gallery module | No MicrosoftDocs source | [Third-Party Modules](#third-party-modules) |

Disambiguation rule: a cmdlet whose noun names a Windows server role or feature
(AD, DNS, DHCP, Hyper-V, Defender, BitLocker, Failover Clustering, Storage, NetTCPIP)
lives in `windows-powershell-docs`, not `PowerShell-Docs`. The core repo covers only
the twelve engine modules listed below.

---

## 1. Core PowerShell

**Repository**: `MicrosoftDocs/PowerShell-Docs`
**Branch**: `main` (the `live` branch also resolves; prefer `main`)

**URL template**:

```
https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/main/reference/{version}/{Module}/{Cmdlet}.md
```

**Version folders**:

| Folder | Covers |
|--------|--------|
| `5.1` | Windows PowerShell 5.1 — ships in-box with Windows |
| `7.4` | PowerShell 7.4 LTS |
| `7.5` | PowerShell 7.5 — current stable |
| `7.6` | PowerShell 7.6 — preview |
| `7.7` | PowerShell 7.7 — preview |

See [Version Selection](#version-selection) for which folder to try first.

**Modules under `7.x`** (12): `CimCmdlets`, `Microsoft.PowerShell.Archive`,
`Microsoft.PowerShell.Core`, `Microsoft.PowerShell.Diagnostics`,
`Microsoft.PowerShell.Host`, `Microsoft.PowerShell.Management`,
`Microsoft.PowerShell.Security`, `Microsoft.PowerShell.Utility`,
`Microsoft.WSMan.Management`, `PSDiagnostics`, `PSReadLine`, `ThreadJob`

**Additional modules under `5.1`** (18 total): the above minus `ThreadJob`, plus
`ISE`, `Microsoft.PowerShell.LocalAccounts`, `Microsoft.PowerShell.ODataUtils`,
`Microsoft.PowerShell.Operation.Validation`, `PSScheduledJob`, `PSWorkflow`,
`PSWorkflowUtility`

**Module routing for common cmdlets**:

| Cmdlet pattern | Module |
|----------------|--------|
| `*-Item`, `*-ItemProperty`, `*-Location`, `*-Content`, `*-Path`, `*-Process`, `*-Service`, `*-Computer`, `*-ComputerInfo`, `Test-Connection`, `*-PSDrive`, `*-Clipboard`, `*-TimeZone`, `*-HotFix` | `Microsoft.PowerShell.Management` |
| `*-Object`, `*-Json`, `*-Csv`, `*-Xml`, `Write-*`, `*-Variable`, `*-Member`, `*-Random` | `Microsoft.PowerShell.Utility` |
| `*-Module`, `*-Job`, `*-PSSession`, `Invoke-Command`, `*-History`, `*-Alias` | `Microsoft.PowerShell.Core` |
| `*-Acl`, `*-AuthenticodeSignature`, `*-ExecutionPolicy`, `ConvertTo-SecureString` | `Microsoft.PowerShell.Security` |
| `*-EventLog`, `*-WinEvent`, `*-Counter` | `Microsoft.PowerShell.Diagnostics` |
| `*-CimInstance`, `*-CimSession`, `*-CimClass` | `CimCmdlets` |
| `*-Archive` | `Microsoft.PowerShell.Archive` |

This table is a shortcut for common cases, not an exhaustive index. When a cmdlet
matches no row, list the version folder via the [contents API](#directory-discovery)
rather than guessing a module — a wrong module folder produces a 404 that looks
identical to a nonexistent cmdlet.

**Examples** (verified):

```
https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/main/reference/7.5/Microsoft.PowerShell.Management/Get-ChildItem.md
https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/main/reference/5.1/Microsoft.PowerShell.Utility/Write-Output.md
```

### Conceptual Topics

`about_*` topics live under an `About` subfolder of `Microsoft.PowerShell.Core`:

```
https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/main/reference/{version}/Microsoft.PowerShell.Core/About/{about_Topic}.md
```

Topic names use underscores and Title Case, e.g.
`about_Functions_Advanced_Parameters.md`, `about_Splatting.md`,
`about_CommonParameters.md`, `about_Comparison_Operators.md`.

Narrative guides (style guide, learning material, what's-new) live under
`reference/docs-conceptual/`:

```
https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs/main/reference/docs-conceptual/community/contributing/powershell-style-guide.md
```

---

## 2. Windows PowerShell Modules

**Repository**: `MicrosoftDocs/windows-powershell-docs`
**Branch**: `main`

Covers the ~143 Windows-only modules shipped with Windows Server and RSAT — Active
Directory, DNS, DHCP, Hyper-V, Defender, BitLocker, Storage, Failover Clustering,
networking, and the rest.

**URL template**:

```
https://raw.githubusercontent.com/MicrosoftDocs/windows-powershell-docs/main/docset/{docset}/{Module}/{Cmdlet}.md
```

**Docset folders**: `winserver2025-ps`, `winserver2022-ps`, `winserver2019-ps`,
`winserver2016-ps`

Default to `winserver2025-ps`. Drop to an older docset only when the user names an
older Windows Server version, or when a cmdlet 404s on 2025 because the feature was
removed.

**Examples** (verified):

```
https://raw.githubusercontent.com/MicrosoftDocs/windows-powershell-docs/main/docset/winserver2025-ps/ActiveDirectory/Get-ADUser.md
https://raw.githubusercontent.com/MicrosoftDocs/windows-powershell-docs/main/docset/winserver2025-ps/Defender/Get-MpComputerStatus.md
https://raw.githubusercontent.com/MicrosoftDocs/windows-powershell-docs/main/docset/winserver2025-ps/DnsServer/Get-DnsServerZone.md
```

**Module name casing is significant** — see [Case Sensitivity](#case-sensitivity).
Module folders use the module's own casing: `ActiveDirectory`, `DnsServer`,
`Hyper-V`, `NetTCPIP`, `ScheduledTasks`, `SmbShare`, `Storage`, `BitLocker`,
`Defender`, `DhcpServer`, `FailoverClusters`, `GroupPolicy`, `PKI`, `PrintManagement`,
`ServerManager`, `Wdac`, `WindowsUpdate`.

To confirm a module folder name, list the docset — see [Directory Discovery](#directory-discovery).

---

## 3. PSResourceGet and PowerShellGet

**Repository**: `MicrosoftDocs/powershell-docs-psget`
**Branch**: `main` (the `live` branch also resolves; prefer `main`)

**URL template**:

```
https://raw.githubusercontent.com/MicrosoftDocs/powershell-docs-psget/main/powershell-gallery/{generation}/{Module}/{Cmdlet}.md
```

| Generation folder | Module folder | Cmdlet style |
|-------------------|---------------|--------------|
| `powershellget-3.x` | `Microsoft.PowerShell.PSResourceGet` | `*-PSResource` (modern) |
| `powershellget-2.x` | `PowerShellGet` | `*-Module`, `*-Script` (legacy) |

**Cmdlets in `Microsoft.PowerShell.PSResourceGet`** (complete, verified):
`Compress-PSResource`, `Find-PSResource`, `Get-InstalledPSResource`,
`Get-PSResourceRepository`, `Get-PSScriptFileInfo`, `Import-PSGetRepository`,
`Install-PSResource`, `New-PSScriptFileInfo`, `Publish-PSResource`,
`Register-PSResourceRepository`, `Reset-PSResourceRepository`, `Save-PSResource`,
`Set-PSResourceRepository`, `Test-PSScriptFileInfo`, `Uninstall-PSResource`,
`Unregister-PSResourceRepository`, `Update-PSModuleManifest`, `Update-PSResource`,
`Update-PSScriptFileInfo`

The module overview page is `Microsoft.PowerShell.PSResourceGet.md` in the same folder;
`about_*` topics sit under an `About` subfolder.

**Example** (verified):

```
https://raw.githubusercontent.com/MicrosoftDocs/powershell-docs-psget/main/powershell-gallery/powershellget-3.x/Microsoft.PowerShell.PSResourceGet/Install-PSResource.md
```

---

## 4. Satellite Modules

**Repository**: `MicrosoftDocs/PowerShell-Docs-Modules`
**Branch**: `main`

First-party modules shipped separately from the engine.

**URL template**:

```
https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs-Modules/main/reference/ps-modules/{Module}/{Cmdlet}.md
```

**Module folders**: `AIShell`, `Microsoft.PowerShell.Crescendo`,
`Microsoft.PowerShell.PlatyPS`, `Microsoft.PowerShell.SecretManagement`,
`Microsoft.PowerShell.SecretStore`, `PSScriptAnalyzer`, `platyPS`

**Example** (verified):

```
https://raw.githubusercontent.com/MicrosoftDocs/PowerShell-Docs-Modules/main/reference/ps-modules/PSScriptAnalyzer/Invoke-ScriptAnalyzer.md
```

---

## Version Selection

The same cmdlet documents differently across version folders. Fetching the wrong one
yields syntax that is authentic but wrong for the target shell — a quieter failure than
a 404, because nothing signals the mismatch.

Read the edition from context before choosing a folder:

| Signal in the request | Version folder |
|-----------------------|----------------|
| `#Requires -Version 5.1`, ISE, `PSWorkflow`, `PSScheduledJob` | `5.1` only |
| `#Requires -Version 7.x`, `pwsh`, Linux/macOS, ternary or `??` operators | matching `7.x` only |
| Long-term-support constraint stated | `7.4` only |
| **No signal** | **fetch `7.5` and `5.1`** |

When both are fetched, compare the parameter tables and state any difference explicitly
rather than silently presenting one edition's syntax:

> `-SkipHttpErrorCheck` requires PowerShell 7; on Windows PowerShell 5.1, wrap the call
> in `try`/`catch` and inspect `$_.Exception.Response.StatusCode` instead.

Skip the second fetch whenever the edition is already pinned.

### Known Cross-Edition Differences

Verified parameters absent from Windows PowerShell 5.1:

| Cmdlet | PowerShell 7 only |
|--------|-------------------|
| `Invoke-RestMethod`, `Invoke-WebRequest` | `-SkipHttpErrorCheck` |
| `ConvertTo-Json` | `-EnumsAsStrings` |
| `ConvertFrom-Json` | `-AsHashtable` |

Module-level differences: `ThreadJob` exists only under `7.x`; `ISE`,
`Microsoft.PowerShell.LocalAccounts`, `PSWorkflow`, `PSWorkflowUtility`, and
`PSScheduledJob` exist only under `5.1`. A 404 in one edition is frequently a
successful fetch in the other — retry before concluding a cmdlet is undocumented.

---

## Directory Discovery

`raw.githubusercontent.com` serves blobs only. Any URL ending in `/` returns **404** —
there is no directory-listing endpoint. Never construct a raw URL to a folder.

To enumerate a folder, fetch the GitHub contents API, which returns plain JSON that
WebFetch parses without difficulty:

```
https://api.github.com/repos/{owner}/{repo}/contents/{path}
```

**Example** — list every module folder in the Windows Server 2025 docset:

```
https://api.github.com/repos/MicrosoftDocs/windows-powershell-docs/contents/docset/winserver2025-ps
```

**WebFetch prompt**: `List the "name" value of every entry whose "type" is "dir".`

The unauthenticated API allows 60 requests per hour per IP. Use it to resolve an
unknown folder name, then fetch the raw markdown directly — do not enumerate a folder
when the exact filename is already known.

---

## Case Sensitivity

Raw GitHub paths are case-sensitive; `learn.microsoft.com` URLs are not. A path that
lowercases a module name resolves on Learn and 404s on raw:

| Result | URL |
|--------|-----|
| **404** | `.../winserver2025-ps/activedirectory/Get-ADUser.md` |
| **200** | `.../winserver2025-ps/ActiveDirectory/Get-ADUser.md` |

Preserve the exact casing of both the module folder and the cmdlet filename. Cmdlet
filenames always match the cmdlet's own `Verb-Noun` casing.

---

## Third-Party Modules

Modules published to the PowerShell Gallery by anyone other than Microsoft have no
MicrosoftDocs source. Resolve them in this order:

1. **Gallery listing** — `https://www.powershellgallery.com/packages/{ModuleName}`
   for version, publish date, download count, and deprecation status.
2. **Project repository README** — the Gallery page's Project Site link, converted to
   a raw URL (see [URL Conversion](#url-conversion)).
3. **WebSearch** — when neither resolves.

---

## URL Conversion

To turn any GitHub page URL into a fetchable raw URL:

```
https://github.com/{owner}/{repo}/blob/{branch}/{path}
         ↓
https://raw.githubusercontent.com/{owner}/{repo}/{branch}/{path}
```

Drop the `/blob/` segment and swap the host. A `github.com` URL fetched directly
returns the surrounding HTML page, not the file.

---

## Rendered Documentation

Use `learn.microsoft.com` only when no raw markdown source exists, or when a raw fetch
has already failed. These pages render from the repositories above, so they are never
more current than the markdown and cost considerably more to retrieve.

| Resource | URL |
|----------|-----|
| PowerShell documentation home | https://learn.microsoft.com/en-us/powershell/ |
| Module browser (all modules) | https://learn.microsoft.com/en-us/powershell/module/ |
| PSResourceGet reference | https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.psresourceget/ |
| PowerShell Gallery | https://www.powershellgallery.com |

---

## Troubleshooting a 404

Work through these in order:

| Check | Fix |
|-------|-----|
| URL ends in `/` | Raw serves no directory listings — use the [contents API](#directory-discovery) |
| Module folder lowercased | Restore exact casing — see [Case Sensitivity](#case-sensitivity) |
| Wrong repository | A Windows role cmdlet is not in `PowerShell-Docs` — re-route via [Source Selection](#source-selection) |
| Wrong version folder | Cmdlet may be 5.1-only (`PSWorkflow`) or 7-only (`ThreadJob`) — try the other edition |
| Wrong module folder | List the version folder via the contents API |
| `github.com` host | Convert to `raw.githubusercontent.com` — see [URL Conversion](#url-conversion) |
| Cmdlet genuinely undocumented | Fall through to WebSearch, then state the uncertainty |
