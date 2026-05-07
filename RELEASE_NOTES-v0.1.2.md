# Gaming Status Bench v0.1.2

Second feedback-focused update. This release makes the tool more useful as a
solver, not just a scanner.

## What Changed

- Added a `FixGuide` section to JSON and HTML reports.
- Every relevant finding can now include:
  - `FixSummary`
  - `FixSteps`
  - `FixCommands`
  - `RequiresAdmin`
  - `RequiresReboot`
  - `SolverAction`
  - `Risk`
- Added a dedicated `fixes` tab in the GUI.
- Added `COPY FIXES` to copy command-backed fixes from the latest report.

## Solver / Unlocker

- Added `GAME DVR OFF` to disable Windows capture/replay-buffer registry values
  for clean benchmark runs.
- Added `GAME MODE ON` for systems where Game Mode is disabled/custom.
- Added `OPEN CAPTURES` to open Windows Captures settings.
- Added `POWER SHOW ALL` to copy `powercfg /qh SCHEME_CURRENT` output.
- Added `POWER UNHIDE ALL` to expose hidden Windows Advanced Power Settings UI
  entries after explicit admin confirmation.
- Power unlocker changes visibility attributes only. It does not change active
  AC/DC power values.

## Network / MTU

- Added local interface MTU checks.
- Added DF-ping Path MTU probes.
- 1500-byte Ethernet paths are marked as `KEEP`.
- 1492-byte paths are treated as `OPTIONAL` and commonly normal for PPPoE.
- ICMP/DF blocked targets are not treated as proof of bad networking.

## Game Ping Profiles

- Added optional game-route probe profiles:
  - `cs2`
  - `valorant`
  - `apex`
  - `cod`
  - `bf6`
  - `fortnite`
  - `trackmania`
  - `tracknations`
  - `marvel-rivals`
  - `rainbow-six-siege`
  - `all`
- Fortnite uses Epic's public datacenter ping endpoints.
- Other profiles use publisher/service route probes because exact match servers
  are dynamic, relay-based or not publicly ICMP-pingable.
- Game profile targets are capped at 3 ping samples per target to keep scans
  from running too long.
- Game/profile targets that do not answer ICMP are shown as `INFO`, not as a
  fix-required network problem.

## GUI Improvements

- Added a game ping profile selector.
- Solver tab now references the FixGuide and power unlocker flow.
- Larger Solver button area for the new actions.
- Findings and FixGuide stay read-only until a user explicitly clicks a Solver
  action with confirmation.

## Trust / Packaging

- No installer.
- No EXE wrapper.
- Plain PowerShell scripts inside the ZIP.
- SHA256 checksum provided.
- Source-available under the PolyForm Strict License 1.0.0.

## Download

Use the release asset:

```text
GamingStatusBench-v0.1.2.zip
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
