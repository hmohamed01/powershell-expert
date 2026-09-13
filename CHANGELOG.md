# Changelog

All notable changes to the powershell-expert skill are documented in this file.

## [1.2.0] - 2026-09-12

### Added
- `references/doc-sources.md` — routing map covering all four MicrosoftDocs repositories, with verified URL templates, module inventories, case-sensitivity rules, and a 404 troubleshooting table
- Raw markdown sources for `MicrosoftDocs/PowerShell-Docs` (core cmdlets and `about_*` topics), `MicrosoftDocs/windows-powershell-docs` (143 Windows-only modules), and `MicrosoftDocs/PowerShell-Docs-Modules` (PSScriptAnalyzer, SecretManagement, Crescendo, PlatyPS)
- Version-selection policy: pin the edition from context, and with no signal fetch both `7.5` and `5.1` and state parameter differences
- Documented cross-edition deltas (`-SkipHttpErrorCheck`, `-EnumsAsStrings`, `-AsHashtable`) verified against live docs
- GitHub contents API as the directory-discovery step, replacing WebSearch for unknown folder names
- PSResourceGet cmdlets with no legacy equivalent (`Compress-PSResource`, `Import-PSGetRepository`, `Reset-PSResourceRepository`, `Update-PSModuleManifest`, the `*-PSScriptFileInfo` set)

### Changed
- Live Verification reworked into Documentation Lookup: raw markdown via WebFetch is now the default first step, with a five-stage fallback chain that reaches WebSearch only when a path cannot be derived
- Repository branch references moved from `live` to `main` throughout
- Reference files and scripts now listed in tables at the end of SKILL.md

### Fixed
- Removed three raw GitHub directory URLs that returned 404 — `raw.githubusercontent.com` serves blobs only and has no directory-listing endpoint
- `Save-PSResource -IncludeXml` was documented as including dependencies; it includes the PSGetModuleInfo.xml metadata file
- `Search-Gallery.ps1` passed `-Type` and `-Prerelease` to `Find-Module`, which accepts neither; the legacy path now uses `Find-Script` for scripts and `-AllowPrerelease`
- `Search-Gallery.ps1` left `$useLegacy` unassigned when PSResourceGet was present, failing under `Set-StrictMode`
- `Ensure-Module` example renamed to `Initialize-RequiredModule` — `Ensure` is not an approved verb

## [1.1.0] - 2026-04-12

### Changed
- Module Recommendations table now requires verification via Live Verification workflow before recommending
- Documentation Resources URLs replaced with raw GitHub markdown equivalents for direct WebFetch parsing
- PSResourceGet docs link updated to raw `MicrosoftDocs/powershell-docs-psget` repository URL
- Gallery Status link (`aka.ms/psgallery-status`) replaced with raw GitHub URL to eliminate redirect
- Gallery Issues link (`aka.ms/psgallery-issues`) replaced with direct GitHub Issues URL

### Added
- Raw GitHub URL pattern for PSResourceGet cmdlet docs in Live Verification Step 2
- Raw PSResourceGet docs URL in `powershellget.md` useful links

## [1.0.0] - 2026-04-11

### Added
- Initial skill with SKILL.md, references, and scripts
- Best practices reference (naming, parameters, pipeline, error handling)
- GUI development reference (Windows Forms, WPF, controls, templates)
- PowerShellGet & Gallery reference (PSResourceGet cmdlets)
- Search-Gallery.ps1 helper script
- Live verification workflow with WebFetch/WebSearch tool instructions
- Fallback strategies for offline verification
