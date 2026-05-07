# Gaming Status Bench v0.1.1

Feedback-focused update after the first public release.

## What Changed

- Added an Action Guide so users can understand what to do next:
  - `MUST FIX`
  - `SHOULD FIX`
  - `REVIEW`
  - `OPTIONAL`
  - `KEEP`
- Added concrete action text to findings in JSON, HTML and GUI output.
- Added a decision legend to the console, HTML report and Solver tab.
- Added a short "what does this tool do?" explanation.
- Clarified that the normal scan is read-only and does not tune Windows by
  itself. Only explicit Solver actions change settings.

## Timer Improvements

- Renamed the old `Global timer-resolution requests` finding to
  `Legacy global timer mode`.
- `GlobalTimerResolutionRequests` not being set is no longer shown as a scary
  warning by itself. It is optional unless an old external tool/game still wakes
  around `15.6 ms`.
- Timer analysis now separates real `15.6 ms` coarse wake problems from smaller
  Windows scheduling jitter.
- `useplatformtick=yes` is now treated as a `SHOULD FIX` baseline issue unless
  the user measured a benefit on that exact PC.
- `useplatformclock=No` is now shown as clean/not forced.

## Fullscreen/Game Improvements

- Added a separate `GameDVR background recording` check for Windows "Record
  what happened" / historical capture.
- If background recording is enabled, the Action Guide recommends disabling it
  before clean benchmarks.

## Display/GPU Fixes

- Fixed MPO detection for `OverlayTestMode=5`.
- The tool now checks the common DWM registry path:
  `HKLM\SOFTWARE\Microsoft\Windows\Dwm`
- It also keeps the GraphicsDrivers path as compatibility fallback.

## GUI Improvements

- Output/tabs area is wider and starts more centrally in the tool window.
- Scan timeout reduced to 2 minutes.
- Solver tab shows the Action Guide verdict and decision counts.
- GUI hint now explains that scans are read-only.

## Trust / Packaging

- No installer.
- No EXE wrapper.
- Plain PowerShell scripts inside the ZIP.
- SHA256 checksum provided.
- Source-available under the PolyForm Strict License 1.0.0.

## Download

Use the release asset:

```text
GamingStatusBench-v0.1.1.zip
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
