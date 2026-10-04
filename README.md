# Gaming Status Bench

Current release: `v0.1.5`

Version 0.1.5 reorganizes the GUI, adds a configurable background timer
holder and checks overlapping timer startups. Validation records 139 passing
assertions; automatic current-user Startup BAT execution after a real sign-in
is still unverified. SYSTEM boot evidence is separate and does not satisfy that
test. See `RELEASE_NOTES-v0.1.5.md` for changes and validation limits.

A transparent Windows gaming diagnostics bench for CPU, USB, timers, network,
display/GPU and latency-critical system state. No installer. No EXE wrapper.
Readable PowerShell. SHA256 verified.

Download: [GamingStatusBench-v0.1.5.zip](https://github.com/MEINWAL/gaming-status-bench-release/releases/download/v0.1.5/GamingStatusBench-v0.1.5.zip)
and [SHA256 checksum](https://github.com/MEINWAL/gaming-status-bench-release/releases/download/v0.1.5/GamingStatusBench-v0.1.5.zip.sha256.txt).
See the [v0.1.5 release](https://github.com/MEINWAL/gaming-status-bench-release/releases/tag/v0.1.5)
for changes and validation limits. Extract the ZIP and run `Run-GamingStatus-GUI.bat`.

Short version: Gaming Status Bench is a Windows gaming/latency health check. It
does not promise magic FPS; it finds common setup problems before benchmarking
and tells you what to keep, review, or fix.

The normal scan does not write persistent Windows settings. Wake benchmarks
temporarily change the collector's own timer request/policy and release that
request afterward. Persistent settings change only through explicit,
confirmed actions; administrative changes require elevation.

`tools/gaming-status/GamingStatus.ps1` creates a local Windows gaming-status
report. It is meant for quick PC-to-PC checks before benchmarking or tuning.

It shows:

- fullscreen/game policy: Game Mode, GameDVR capture/background recording,
  global FSO/FSE registry values, per-game AppCompat flags such as
  `DISABLEDXMAXIMIZEDWINDOWEDMODE`
- running window geometry guess for game windows
- timer resolution through `NtQueryTimerResolution`
- short 1 ms sleep jitter benchmark
- Windows 11 per-process timer-resolution policy, including the
  `IGNORE_TIMER_RESOLUTION` state that can make a process wake at ~15.6 ms
  even while the reported timer resolution is ~0.5 ms
- a timer analysis matrix with OK/WARN/FAIL thresholds for reported
  resolution, `Sleep(1)`, waitable timers, global timer mode and remaining
  jitter
- an action guide that labels each finding as `KEEP`, `OPTIONAL`, `REVIEW`,
  `SHOULD FIX` or `MUST FIX`, with a concrete next step
- a fix guide that adds solution steps, command examples, admin/reboot flags
  and risk notes for important findings
- CPU topology checks for visible logical processors, SMT/Hyper-Threading
  visibility and active `numproc` boot limits
- BCDEdit values such as `disabledynamictick`, `useplatformclock`,
  `useplatformtick`
- power plan state
- power-plan unlocker visibility: hidden Advanced Power Settings count plus
  `powercfg /qh SCHEME_CURRENT` show-all output
- USB controller/device power-saving state, including registry fallback checks
  when the WMI backend is unavailable
- GPU/display basics, HAGS and MPO registry state, optional `nvidia-smi`
- Display/GPU cleanup separates real active display outputs from basic or
  incomplete WMI adapters so phantom `x@Hz` rows do not become top-level action
  items
- network lite report: NIC name, driver model hint, driver version, link speed,
  global RSS state, adapter RSS visibility, interrupt moderation, EEE/Green
  Ethernet, power-saving flags, offload summary and a safe
  `OK`/`REVIEW`/`MANUAL TEST` recommendation
- network state: `netsh` TCP/IP output, adapters, RSS/RSC/offload settings,
  NIC advanced properties, DNS, local interface MTU, DF-ping path MTU probes,
  ping latency/loss/jitter
- optional game ping profiles for CS2, VALORANT, Apex, Call of Duty,
  Battlefield, Fortnite, Trackmania, Trackmania Nations, Marvel Rivals and
  Rainbow Six Siege
- security latency context: VBS / memory integrity where readable
- common overlay/helper processes

### Run

Graphical Windows UI:

```text
Run-GamingStatus-GUI.bat
```

CLI/console report:

```text
Run-GamingStatus.bat
```

Or run from PowerShell:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\gaming-status\GamingStatus.ps1 -OpenHtml
```

Game-route probes can be added with:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\gaming-status\GamingStatus.ps1 -GamePingProfile cs2,valorant,fortnite -OpenHtml
```

Known profiles: `cs2`, `valorant`, `apex`, `cod`, `bf6`, `fortnite`,
`trackmania`, `tracknations`, `marvel-rivals`, `rainbow-six-siege`, or `all`.
These are route/service probes, not guaranteed exact in-match server pings.
Fortnite uses Epic's public datacenter ping endpoints; most other games use
publisher/service endpoints because exact match servers are dynamic or not
publicly ICMP-pingable. Game profile targets are capped at 3 ping samples per
target to keep scans from running too long.

For exact per-game FSO checks, pass one or more game exe paths:

```powershell
.\tools\gaming-status\GamingStatus.ps1 -GameExePath "C:\Games\Counter-Strike Global Offensive\game\bin\win64\cs2.exe" -OpenHtml
```

Reports are written to `reports\` as JSON and HTML.

MPO detection checks `OverlayTestMode=5` under the common
`HKLM\SOFTWARE\Microsoft\Windows\Dwm` location, plus the GraphicsDrivers path
for compatibility. `OverlayTestMode=5` is reported as MPO disabled.

For BCDEdit timer settings, forced `useplatformclock`/HPET and forced
`useplatformtick` are treated as values to fix unless the user has measured a
benefit on that exact PC.

### Read The Result

The report is meant to answer "do I need to change anything?" directly:

- `MUST FIX`: change this before benchmarking; it can invalidate results
- `SHOULD FIX`: fix before trusting latency/FPS comparisons
- `REVIEW`: retest idle or verify manually before changing anything
- `OPTIONAL`: only change for A/B tests or a specific tuning goal
- `KEEP`: value/state is good; leave it alone

For timers, the important bad sign is a `Sleep(1)` or waitable timer p95 near
`15.6 ms`. After global timer mode is fixed, remaining `~1.0-2.5 ms` wake
behavior is normally a good Windows desktop result.

For competitive timer chasing, the target is the lowest supported value reported
by `NtQueryTimerResolution`. On many gaming PCs that is about `0.5 ms`, so a
current value around `0.511 ms` is already effectively at the practical minimum.
The tool now reports `current` vs `lowest supported` directly. If current is not
at the desired target, open Timer, enter a supported target, and use `Start
holder`. Compare requested, API-returned and live measured resolution. Only
consider legacy global mode plus reboot for a demonstrated compatibility issue.
The app does not guarantee that another process or game uses the same interval.

### GUI Workspace

The GUI has Overview, Timer, CPU, USB, Network, Display/GPU and Power/Game
sections. Actions sit with their subject's measurements. Status and search
filters narrow findings; selecting a finding separates Diagnosis, Technical
details and FixCommands. Logs and Report JSON have dedicated views.

- Overview: report summary, HTML/JSON exports, report folder and summary copy.
- Timer: configurable background holder, Start/Stop, GUI-only process policy,
  optional sign-in autostart, legacy global mode and timer boot baseline.
- CPU: topology/SMT visibility, gated 16-thread boot cap and cap removal.
  Neither action enables BIOS SMT. A fresh report is required.
- USB: matched device power flags and confirmed, read-back-verified changes.
- Network: route/MTU/NIC diagnostics, command copies, settings shortcuts and
  optional DHCP/DNS refresh: release, wait 6s, renew, wait 6s, flush DNS.
- Display/GPU: active-mode diagnostics and Windows settings shortcuts.
- Power/Game: full plan output copy, power-setting visibility, Game Mode and
  capture/replay settings. Unhiding preserves other Attributes bits and does
  not change active power values.

Command-copy and settings-opening actions do not apply those settings. Running
actions disable conflicting controls. Writes are checked by exit status and
read-back, where available. Failures remain visible; partial writes are not
reported as success. Boot changes remain marked reboot-required across GUI
restarts until a changed Windows boot timestamp is observed.
Command-copy controls are unavailable when their selected finding or section has
no runnable commands; stale CPU safety filters still apply. A persistent GUI
privilege label is independent of the elevation recorded in the loaded report.
Restarting as administrator preserves the timer scope, target, report and section;
the launch is reported as requested, not as proven elevation. Machine mode ignores
user BAT overrides while inspecting its own task, and preserves the override for
switching back to user mode. Directory aliases with trailing separators use the
same holder singleton and stop-event identity.

Report measurements are snapshots, not live state. Changes mark them stale;
loading a previous report does not make it fresh. `Rescan without network`
collects full diagnostics with network probes skipped, not a timer-only test.
Sleep/wait tests make their own fine timer request and cannot prove that a game
has the same wake behavior. The app does not measure game FPS, frametimes or
end-to-end input latency.

### Timer Holder And Autostart

Targets use 100-nanosecond units: `5070` means `0.5070 ms`. The supported range
is queried before launch. Requested, API-returned and live measured values are
shown separately; any difference is a mismatch, not a successful exact setting.
An existing finer request from another process can affect the measured value.

The holder is an independent readable PowerShell process. Repeated Start does
not stack requests. Stop releases its matching request. Closing the GUI does
not stop the holder; stopping it does not remove other processes' requests.

Current-user autostart copies `TimerHolder.ps1`, `TimerRuntime.ps1` and
`TimerMachine.ps1` into
`%LOCALAPPDATA%\GamingStatusBench\Timer` and creates
`GamingStatusBench-Timer.bat` in the current user's Startup folder. It starts
at Windows sign-in, not before sign-in or simply at boot. Inspect shows the
BAT, configuration and installed script hashes. Enabling/updating autostart
does not start or change a running holder. Disabling removes the BAT but does
not stop an already running holder. Windows Startup management can independently
disable an entry; a verified BAT/configuration is not proof of sign-in execution.
Machine startup is a separate, explicitly selected mode. Administrator approval
installs protected scripts/configuration in
`%PROGRAMDATA%\GamingStatusBench\TimerMachine` and registers
`GamingStatusBench-Timer-Machine` with boot/logon triggers as SYSTEM. It runs in
session 0 without requiring an elevated desktop account. Only SYSTEM and
Administrators can write the scripts/configuration; standard users can inspect
status. Start/Stop/configuration changes require administrator rights.
The task ignores duplicate launches and has no runtime time limit. Its holder
uses a machine-wide singleton and survives signing out.

Switching to machine startup backs up and removes the managed user Startup BAT.
Recognized `SetTimerResolution.exe` Run entries/processes and the verified
`TimerResolutionStartup` SYSTEM task must be explicitly replaced before starting
a second holder. The verified legacy task is backed up and disabled, not deleted.
Backups are local; arbitrary startup scripts, other third-party tasks and services
are not automatically removed.
Machine target updates stop an existing managed holder; start it again after
updating. Disabling machine startup disables its task, not a running holder.
Startup failures are retained in `startup-error.txt`, including failures before
the first heartbeat. Task registration/manual launch is not proof of actual boot
execution. No Windows service or EXE wrapper is installed.

From an elevated PowerShell, machine configuration can also be managed with:

```powershell
.\tools\gaming-status\Manage-TimerStartup.ps1 -Action InstallMachine -Target100ns 5070 -ReplaceLegacy -StartNow
.\tools\gaming-status\Manage-TimerStartup.ps1 -Action Inspect
.\tools\gaming-status\Manage-TimerStartup.ps1 -Action Stop
.\tools\gaming-status\Manage-TimerStartup.ps1 -Action DisableMachine
```

`-ReplaceLegacy` authorizes backup/removal of recognized Run entries, backup/disable
of the verified legacy timer task, and stopping its matching processes; omit it to
refuse coexistence.
Installation captures a pre-reboot baseline. After a real reboot, run
`tests\Test-TimerMachineStartup.ps1` to check SYSTEM identity, session 0, target,
fresh heartbeat, boot timestamp and launch timing without manually starting it.

### Timer Startup Health Check

Use **Timer > Check startup conflicts**, or run a normal scan. The read-only check
inventories known timer launchers in scheduled tasks, machine/current-user
Run/RunOnce entries, Startup folders (including shortcut targets), services and
running processes. It reports configured targets in 100 ns units, disabled entries,
task identities and process/session IDs. It flags multiple active entries/processes,
different targets, invalid GSB startup, stale holder state, target mismatches and
machine startup configured before boot with no verified holder after a grace period.

Protected tasks and process arguments can be hidden from standard users. Incomplete
or failed reads produce **UNKNOWN / CHECK**, not an all-clear. Elevation improves
visibility but does not turn a failed source into a successful check. Results and
per-source errors are included in `Sections.Timer.StartupDiagnostics` and HTML.
The GUI check is timestamped separately from live resolution and last-scan findings.
Run values and Startup files are candidates, not proof of Windows execution
approval. Their `Enabled` value is unknown (`null`) and startup approval remains
an explicit CHECK; candidate counts include these unverified entries.

This is not an inventory of every kernel timer request. Other users, arbitrary or
encoded launcher scripts and unrecognized tools may be missed. Multiple requests
do not by themselves prove a jitter cause. The check never changes a timer request,
disables a task, stops a process or applies generated commands. Review findings,
then explicitly choose any replacement or manual change. Boot success still needs
verification after an actual boot.

Reports also include a `FixGuide` section. It gives every relevant finding a
plain fix summary, step order, command examples where a safe command exists,
and `RequiresAdmin` / `RequiresReboot` / `Risk` fields. The GUI exposes diagnosis
and commands separately for the selected finding, with subject-specific command
copies. Nothing copied is automatically executed. JSON fix fields such as
`FixSteps` and `FixCommands` are emitted
as arrays, including empty or single-item cases, so downstream tools can parse
them consistently.

Reports include a `ReportSchemaVersion` and a `ReportContract` section. The
contract documents array-shaped JSON fields and Display/GPU reporting rules so
external parsers can treat the report as structured data instead of scraping
HTML or console output.

Diagnosis and commands are separated in the report:

- `Analysis.DiagnosisGuide` is read-only diagnosis and recommended next action.
- `Analysis.CommandGuide` contains only command-backed fixes, including the
  diagnostic value/note that justified each command group.
- `Findings[].FixCommands` remains for backward compatibility with older
  tooling.

The package also includes:

- `VERIFY.ps1` for local package checks and optional ZIP/SHA256 verification
- `sample-report.json` as a small, sanitized schema/example report
- `PRIVACY.md` describing what the tool reads, what it writes and what to check
  before sharing reports

### Build A Release Package

The release ZIP is built from a fixed file list. To create the same local
package layout used for GitHub releases, run:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\release\Build-GamingStatusRelease.ps1 -Version 0.1.5
```

The build script validates PowerShell syntax, version strings, README/release
notes text, ZIP contents and SHA256 output. It only writes to `dist\`; it does
not push Git commits or publish a GitHub release.

After extracting a release ZIP, run:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\VERIFY.ps1
```

To verify a downloaded ZIP against its checksum file:

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\VERIFY.ps1 -ZipPath ..\GamingStatusBench-v0.1.5.zip -Sha256Path ..\GamingStatusBench-v0.1.5.zip.sha256.txt
```

`Unhide advanced power settings` only changes visibility attributes under
`HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerSettings`. It does not change
active AC/DC power values. Use `Copy full power plan output` first, then change actual power
values one at a time and retest.

### Windows Validation

```powershell
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\gaming-status\tests\Test-GamingStatusWorkspace.ps1
powershell.exe -NoProfile -ExecutionPolicy Bypass -File .\tools\gaming-status\tests\Test-GuiActionFeedback.ps1
```

This uses isolated temporary holder/Startup paths, renders Windows UI
screenshots and runs a collector scan. Evidence stays under `reports\ui-qa`.
It does not change real boot, USB, power or network settings. A manual launch
of the exact Startup BAT is tested, but a real sign-out/sign-in check remains
a separate required validation step. Package/syntax/SHA checks are not proof
that every workflow or actual sign-in execution has been tested.

The action-feedback suite simulates boot/network commands and uses temporary
HKCU test keys for real registry read-back tests; it never changes real device
or boot settings. It checks cancellation, command failure, stale CPU gating,
reboot receipts, visibility bit preservation and DHCP recovery sequencing.
It also checks clipboard read-back and managed user/machine timer conflicts:
starting a second managed scope is blocked, while stopping a selected holder
or disabling its existing startup remains available.
GUI reboot receipts are independent of managed timer scope. Older receipts are
still read; expired protected receipts need not be deleted, and failed reads
leave an explicit unverified warning.

For an explicitly approved real sign-in test, `tests\Test-TimerSignIn.ps1
-Prepare` records a baseline logon identity. After enabling the Startup BAT and
signing out/in manually, run the script without `-Prepare`. It checks the
configured BAT, running holder, target/policy and a new interactive logon
session after preparation; a changed PID alone is not sufficient evidence.
This script always defaults to the current-user installation and rejects
machine scope. Missing or invalid baselines remain `PENDING`. Its `SignIn`
launch marker is not proof that Windows launched the BAT automatically:
observe actual automatic execution after signing in, without manually launching
the BAT. SYSTEM boot validation uses `Test-TimerMachineStartup.ps1` separately.

### Notes

Run as administrator for the most complete network, adapter and BCDEdit
visibility.

Windows does not expose one perfect external "this game is FSE vs FSO right
now" flag. The tool therefore reports three separate signals: global policy,
per-exe compatibility flags, and the running window geometry. Treat that as a
status panel and compare it with in-game/PresentMon/CapFrameX measurements.

On Windows 10 2004+ and Windows 11, a fine reported timer resolution does not
guarantee that every process wakes at that interval. The affected process needs
its own timer request, and on Windows 11 may also need the per-process
`IGNORE_TIMER_RESOLUTION` policy cleared. Windows 11 also has an optional
legacy/global compatibility switch, `GlobalTimerResolutionRequests=1`, for
older apps/tests that never make their own request; reboot after changing it.
If the report says that legacy global timer mode is not set, that alone is not
a game-performance problem. It only becomes important when a specific external
tester or old game still shows `Sleep(1)` around `15.6 ms`.

### License

This project is source-available under the PolyForm Strict License 1.0.0.

Allowed: personal and noncommercial use for benchmarking, testing, research,
study, private entertainment and hobby work.

Not allowed without a separate written license: commercial use, redistribution,
modified versions, derivative tools, or use as the basis for a commercial
product or service.
