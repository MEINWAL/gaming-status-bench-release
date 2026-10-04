# Gaming Status Bench v0.1.5

Release package: `GamingStatusBench-v0.1.5.zip`.

A transparent Windows gaming diagnostics bench for CPU, USB, timers, network,
display/GPU and latency-critical system state. No installer. No EXE wrapper.
Readable PowerShell. SHA256 verified.

This update focuses on a clearer workspace, configurable timer holding,
transparent startup inspection and verified action feedback. It does not
guarantee better FPS, lower input latency or identical Sleep wake times.

## UI/UX

- Subject workspace: Overview, Timer, CPU, USB, Network, Display/GPU, Power/Game.
- Status/search filters; separate diagnosis, technical details, commands and logs.
- Descriptive, unique action labels; neutral controls and labeled status colors.
- Busy guards, stale measurements, read-back checks and retained reboot warnings.
- Live-query failures clear old timer values and disable conflicting holder controls.
- Explicit UAC cancellation/failure feedback and consistent scan status colors.
- Administrator relaunch preserves scope, timer target, report and section,
  records feedback before closing, and never claims elevation was verified.
- Actual GUI privileges are labeled separately from historical scan privileges.
- Command-copy controls follow real command availability and existing safety gates.
- Summary copies verify clipboard read-back before reporting success.
- Reboot warnings survive switching managed timer scope. Protected expired
  legacy receipts are ignored without deletion; malformed reads stay unverified.
- Display summary uses active modes instead of the first raw WMI adapter.

## Timer

- Configurable background timer holder, including `5070` (`0.5070 ms`).
- Requested, API-returned and measured resolution shown independently.
- Matching Start/Stop with single-instance protection and process identity checks.
- Optional current-user sign-in Startup BAT, inspect/update/disable controls.
- Separate protected machine-startup mode: SYSTEM boot/logon task, session-0
  holder, admin-only configuration/control, and standard-user status inspection.
- Machine-wide singleton and scheduler duplicate prevention; unlimited runtime.
- Trailing path separators resolve to the same holder/signal identity; native
  launch arguments preserve quote-adjacent and trailing backslashes.
- Visible managed-scope conflict guards block duplicate user/machine starts
  and user startup enablement. Unknown other-scope state is not an all-clear;
  stopping the selected holder and disabling its existing startup remain available.
- Switching standard timer scopes updates the actual holder path, including an
  explicit machine path. Custom directories have a fixed, clearly labeled scope.
- Machine-mode status cannot be masked by a current-user Startup BAT override.
- Explicit backup/replacement of recognized legacy Run entries/processes and
  removal of the managed user BAT when switching to machine startup.
- Persistent startup error reporting, including pre-heartbeat failures.
- Elevated legacy scheduled-task inventory and backup/disable migration for the
  recognized TimerResolutionStartup runner; standard-user inspection explicitly
  marks protected-task coverage as incomplete.
- Readable installed scripts with hash checks; no service or EXE wrapper.
- Read-only timer startup health check in the Timer workspace and JSON/HTML:
  tasks, Run/RunOnce, Startup folders/shortcuts, services and timer processes.
  Flags overlapping startups, different targets, invalid/stale GSB state and
  request/runtime mismatches. Protected/failed reads cannot produce an all-clear.
  Disabled legacy tasks remain visible without being counted as active conflicts.
- GUI-only policy and holder policy clearly scoped; no guaranteed game FPS benefit.

## Validation

Recorded Windows validation: 139 passing assertions across the workspace (43),
action feedback (56), machine read-only inspection (15), and timer startup
diagnostics (25). Tests cover real isolated timer hold/release, manual BAT launch,
configuration integrity and UI rendering. Risky boot/network commands are
simulated; registry read-back uses isolated HKCU fixtures.

One required test remains pending: automatic current-user Startup BAT execution
after a real Windows sign-in. Manual BAT execution is not equivalent to that
test. A SYSTEM holder was verified after an actual boot on the test PC, but
machine-mode evidence does not validate user BAT sign-in. Startup configuration
does not guarantee the next boot/sign-in will succeed. The full UI/UX acceptance
goal remains incomplete; publication does not mean that this sign-in workflow
has been fully validated.
The Startup BAT sign-in validator defaults to user scope and rejects SYSTEM
scope; missing/invalid baselines remain pending. A launch marker alone does not
prove an automatic Windows Startup trigger.
Native child-process tests cover custom, current-user and machine scope relaunch,
including paths with spaces, ampersands and trailing separators. GUI test harnesses
restore fail-fast error handling after loading the interface; initialization
exceptions cannot silently finish as successful test runs.

## Download / Verify

Use the ZIP and its adjacent checksum file:

```text
GamingStatusBench-v0.1.5.zip
GamingStatusBench-v0.1.5.zip.sha256.txt
```

Extract the ZIP, then run `Run-GamingStatus-GUI.bat`. Run as Administrator for
protected startup-task visibility and actions that require elevation. Changing
timer startup is optional and explicit; opening the app does not install it.

From the extracted package folder:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\VERIFY.ps1 -ZipPath ..\GamingStatusBench-v0.1.5.zip -Sha256Path ..\GamingStatusBench-v0.1.5.zip.sha256.txt
```

## Packaging / Privacy

- Same release-only GitHub layout as previous versions: ZIP, SHA256 and metadata.
- Readable PowerShell and batch files are distributed inside the ZIP; no loose
  application source tree is uploaded to the release repository.
- No installer, EXE wrapper or telemetry. Reports stay local.
- Private reports, screenshots, timer runtime state and backups are excluded.
- `VERIFY.ps1`, sanitized `sample-report.json` and `PRIVACY.md` are included.
- The build script writes local files only; it does not publish to GitHub.

## License

Source-available under the PolyForm Strict License 1.0.0. See `LICENSE` for terms.
