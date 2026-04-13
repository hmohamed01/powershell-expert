# Changelog

All notable changes to the powershell-expert skill are documented in this file.

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
