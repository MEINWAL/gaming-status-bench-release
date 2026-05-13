# Gaming Status Bench

Current release: `v0.1.3`

Short version: Gaming Status Bench is a Windows gaming/latency health check. It
does not promise magic FPS; it finds common setup problems before benchmarking
and tells you what to keep, review, or fix.

The normal scan is read-only. It does not tune Windows automatically. Settings
only change when you use a Solver button that explicitly says it applies a fix,
and those actions ask for confirmation/admin rights where needed.

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
- network state: `netsh` TCP/IP output, adapters, RSS/RSC/offload settings,
  NIC advanced properties, DNS, local interface MTU, DF-ping path MTU probes,
  ping latency/loss/jitter
- optional game ping profiles for CS2, VALORANT, Apex, Call of Duty,
  Battlefield, Fortnite, Trackmania, Trackmania Nations, Marvel Rivals and
  Rainbow Six Siege
- security latency context: VBS / memory integrity where readable
- common overlay/helper processes

### Run

Graphical terminal-style Windows UI:

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
at the target, use the Solver flow: `FIX PROC POLICY`, `HOLD 0.5MS`, retest, and
only use `APPLY GLOBAL MODE` plus reboot when old external tools still sleep at
`~15.6 ms`.

The GUI has buttons for:

- running the read-only scan without leaving the window
- relaunching itself as Administrator
- opening the latest HTML/JSON report
- opening the reports folder
- copying a compact summary

The GUI also has a `solver` tab for common timer-state problems:

- show whether the latest report is a clean or mixed timer state
- clear the GUI process `IGNORE_TIMER_RESOLUTION` policy for retests
- hold a `0.5 ms` timer request while the GUI is open
- run a quick timer-only retest
- enable the optional Windows 11 legacy/global timer mode
  `GlobalTimerResolutionRequests=1` for old tools/games that still sleep at
  ~15.6 ms
- copy recommended timer baseline commands
- copy full current power plan output with `POWER SHOW ALL`
- unhide discovered Windows Advanced Power Settings UI entries with
  `POWER UNHIDE ALL` after explicit admin confirmation
- optionally apply the BCDEdit timer baseline after an explicit admin
  confirmation
- disable Windows GameDVR capture/replay buffer for clean benchmark runs
- enable Windows Game Mode when it is disabled/custom
- open Windows Captures settings for manual verification
- select optional game ping profiles for route/service probes
- review CPU scheduler state, use `CPU RESTORE` for unintended `numproc` caps,
  and only allow `CPU SET 16` when the latest report confirms a safe target
- apply `USB POWER SAVE OFF` for strict input-latency benchmark profiles

Reports also include a `FixGuide` section. It gives every relevant finding a
plain fix summary, step order, command examples where a safe command exists,
and `RequiresAdmin` / `RequiresReboot` / `Risk` fields. The GUI exposes this in
the `fixes` tab and the `COPY FIXES` button copies command-backed fixes from the
latest report. JSON fix fields such as `FixSteps` and `FixCommands` are emitted
as arrays, including empty or single-item cases, so downstream tools can parse
them consistently.

`POWER UNHIDE ALL` only changes visibility attributes under
`HKLM\SYSTEM\CurrentControlSet\Control\Power\PowerSettings`. It does not change
active AC/DC power values. Use `POWER SHOW ALL` first, then change actual power
values one at a time and retest.

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
