# Gaming Status Bench

Current release: `v0.1.0`

Gaming Status Bench is a local Windows gaming-status report tool. It is meant
for quick PC-to-PC checks before benchmarking or tuning.

It shows:

- fullscreen/game policy: Game Mode, GameDVR, global FSO/FSE registry values,
  per-game AppCompat flags such as `DISABLEDXMAXIMIZEDWINDOWEDMODE`
- running window geometry guess for game windows
- timer resolution through `NtQueryTimerResolution`
- short 1 ms sleep jitter benchmark
- Windows 11 per-process timer-resolution policy, including the
  `IGNORE_TIMER_RESOLUTION` state that can make a process wake at ~15.6 ms
  even while the reported timer resolution is ~0.5 ms
- a timer analysis matrix with OK/WARN/FAIL thresholds for reported
  resolution, `Sleep(1)`, waitable timers, global timer mode and remaining
  jitter
- BCDEdit values such as `disabledynamictick`, `useplatformclock`,
  `useplatformtick`
- power plan state
- GPU/display basics, HAGS and MPO registry state, optional `nvidia-smi`
- network state: `netsh` TCP/IP output, adapters, RSS/RSC/offload settings,
  NIC advanced properties, DNS, MTU, ping latency/loss/jitter
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

For exact per-game FSO checks, pass one or more game exe paths:

```powershell
.\tools\gaming-status\GamingStatus.ps1 -GameExePath "C:\Games\Counter-Strike Global Offensive\game\bin\win64\cs2.exe" -OpenHtml
```

Reports are written to `reports\` as JSON and HTML.

The GUI has buttons for:

- running the scan without leaving the window
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
- optionally apply the BCDEdit timer baseline after an explicit admin
  confirmation

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

### License

This project is source-available under the PolyForm Strict License 1.0.0.

Allowed: personal and noncommercial use for benchmarking, testing, research,
study, private entertainment and hobby work.

Not allowed without a separate written license: commercial use, redistribution,
modified versions, derivative tools, or use as the basis for a commercial
product or service.
