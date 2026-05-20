# Gaming Status Bench v0.1.4

A transparent Windows gaming diagnostics bench for CPU, USB, timers, network,
display/GPU and latency-critical system state. No installer. No EXE wrapper.
Readable PowerShell. SHA256 verified.

## What Changed

- Cleaned up Display/GPU reporting so incomplete/basic WMI adapters are kept in
  the raw adapter inventory but no longer become active display-mode findings.
- Added `ActiveDisplayModes`, `SkippedDisplayModeAdapters` and
  `DisplayModeSummary` to the Display/GPU section.
- Added `NetworkLiteReport` for NIC name, driver model context, driver
  version, link speed, global RSS state, adapter RSS visibility, interrupt
  moderation, EEE/Green Ethernet, power-saving flags, offload summary and a
  safe `OK`/`REVIEW`/`MANUAL TEST` recommendation.
- Network driver model is treated as context, not a ranking: NetAdapterCx vs
  NDIS and missing adapter RSS exposure are marked for measurement, not blind
  judgement.
- Added an explicit pre-game network refresher in the GUI solver and command
  guide: `ipconfig /release`, 6 second wait, `ipconfig /renew`, 6 second wait,
  then `ipconfig /flushdns`.
- The network refresher is marked as an optional/manual session action because
  it briefly disconnects networking.
- Added `ReportSchemaVersion`, `GeneratedByVersion` and `ReportContract` to the
  JSON report.
- Documented stable array fields for downstream parsers.
- Added `DiagnosisGuide` as a read-only diagnosis view.
- Added `CommandGuide` as a command-only section for command-backed fixes.
- Kept `Findings[].FixCommands` for backward compatibility.
- Updated the GUI fixes view so diagnosis/steps and runnable commands are shown
  separately; the copy button now targets command output directly.
- Added a release package build script:
  `tools\release\Build-GamingStatusRelease.ps1`
- Added `VERIFY.ps1`.
- Added `sample-report.json`.
- Added `PRIVACY.md`.

## Build / Packaging

- The build script creates the same fixed ZIP layout used by previous releases.
- It validates:
  - PowerShell parser state for the collector and GUI scripts
  - `VERIFY.ps1` parser state
  - collector version string
  - README current-release text
  - release notes title and ZIP name
  - sample report version
  - expected ZIP entries
  - SHA256 output
- The script writes local files under `dist\` only. It does not push Git commits
  or publish a GitHub release.

## Verify / Privacy

- `VERIFY.ps1` checks required package files, PowerShell parser state,
  `sample-report.json`, absence of EXE files and optional ZIP/SHA256 matching.
- `sample-report.json` gives parser authors a small sanitized example with
  schema metadata, findings, `DiagnosisGuide` and `CommandGuide`.
- `PRIVACY.md` documents local data collection, ping probes, locally written
  report files and what users should review before sharing reports.

## Trust / Packaging

- No installer.
- No EXE wrapper.
- Plain PowerShell scripts inside the ZIP.
- SHA256 checksum provided.
- Source-available under the PolyForm Strict License 1.0.0.

## Download

Use the release asset:

```text
GamingStatusBench-v0.1.4.zip
```

Extract it, then run:

```text
Run-GamingStatus-GUI.bat
```

Run as Administrator for the most complete report and for Solver actions that
write registry or BCDEdit settings.

## License

Licensed under the PolyForm Strict License 1.0.0.

This is source-available software for personal and noncommercial benchmarking
use. Commercial use, redistribution, modified versions and derivative tools are
not allowed without a separate written license.
