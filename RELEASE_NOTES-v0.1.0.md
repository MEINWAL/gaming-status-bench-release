# Gaming Status Bench v0.1.0

First public source-available release of Gaming Status Bench.

## Highlights

- Terminal-style Windows GUI for quick gaming/latency system status checks.
- CLI collector that writes JSON and HTML reports.
- Timer checks:
  - `NtQueryTimerResolution`
  - `Thread.Sleep(1)` wake p95
  - waitable timer wake p95
  - Windows 11 process timer policy
  - `GlobalTimerResolutionRequests` status
  - timer analysis matrix with OK/WARN/FAIL thresholds
- Fullscreen/Game checks:
  - Windows Game Mode
  - GameDVR capture state
  - global FSO/FSE policy signals
  - per-game AppCompat flags when an exe path is provided
  - running window geometry hints
- Display/GPU checks:
  - active resolution and refresh rate
  - HAGS
  - MPO
  - optional `nvidia-smi` telemetry
- Network checks:
  - adapter/link state
  - RSS/RSC/offload status
  - selected NIC advanced properties
  - DNS, MTU and ping stats
- Security/overlay context:
  - VBS/HVCI
  - common overlay/helper process watchlist
- Solver tab:
  - process timer policy fix
  - 0.5 ms timer hold
  - optional Windows 11 legacy/global timer mode
  - BCDEdit baseline helper

## Download

Use the release asset:

```text
GamingStatusBench-v0.1.0.zip
```

Extract it, then run:

```text
Run-GamingStatus-GUI.bat
```

Run as Administrator for the most complete report and for solver actions that
write registry or BCDEdit settings.

## License

Licensed under the PolyForm Strict License 1.0.0.

This is source-available software for personal and noncommercial benchmarking
use. Commercial use, redistribution, modified versions and derivative tools are
not allowed without a separate written license.

## Known Notes

- Windows may flag downloaded scripts with Mark-of-the-Web. The included batch
  files start PowerShell with `ExecutionPolicy Bypass`, but some environments
  may still require unblocking the extracted files.
- Some solver actions require Administrator rights and a reboot before retest.
- Windows does not expose a perfect external FSE-vs-FSO truth flag; this tool
  reports policy, per-exe compatibility flags and window geometry signals.
