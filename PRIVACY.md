# Privacy

Gaming Status Bench is a local Windows diagnostics tool. It does not include
telemetry, analytics, tracking, ads, update beacons or a background service.

## What The Tool Reads

The collector reads local system state through Windows APIs and command-line
tools such as WMI/CIM, registry reads, `bcdedit`, `powercfg`, `netsh`,
`ping` and optional `nvidia-smi`.

Reports may include:

- computer name and Windows user name
- Windows version, boot/install timestamps and PowerShell version
- CPU, memory, motherboard/system model and GPU information
- display mode, HAGS/MPO state and optional NVIDIA telemetry
- timer behavior and process timer policy state
- matching timer startup task/service names, commands, configured targets,
  Run/RunOnce values, Startup script content/shortcut targets and timer-launcher
  process/session IDs; per-source visibility errors and managed startup errors
- power plan and USB power-policy state
- network adapter, DNS, MTU and ping probe results
- selected registry/BCDEdit values relevant to gaming diagnostics
- raw command output used to explain findings

## Network Activity

Normal scans may send ICMP ping probes to configured targets, by default public
DNS targets such as `1.1.1.1` and `8.8.8.8`, unless network benchmarking is
disabled. Optional game ping profiles add route/service probes for selected
games. These probes are diagnostics only and are not telemetry sent back to the
author.

The tool does not upload reports.

Timer startup health checks are local and read-only. They inspect machine and
current-user startup sources for known timer signatures; no inspected script or
shortcut is executed. Timer launch commands/script content can include private
paths, account names or arguments, so review this section before sharing a report.

The optional `Refresh DHCP and DNS` action runs local Windows `ipconfig`
commands to release/renew DHCP and flush DNS. It does not contact the author or
upload anything, but it briefly disconnects local network connectivity while the
lease is renewed.

## Files Written

Reports are written locally under `reports\` as JSON and, unless disabled, HTML.
Solver actions only run after the user clicks an explicit button or command.

Starting the timer holder creates a local background PowerShell process and
state/heartbeat JSON under `%LOCALAPPDATA%\GamingStatusBench\Timer`. Its state
includes the PID, process start time, Windows user SID/logon session identifier,
session/boot timestamps, requested and
measured timer resolution, launch context and errors. It does not transmit data.
The holder stays running after the GUI closes until stopped or the session ends.

Optional sign-in autostart copies readable support scripts and configuration
into that directory and writes `GamingStatusBench-Timer.bat` in the current
user's Startup folder. Disabling autostart removes that BAT; it does not stop
an already running holder. Installed support scripts/state remain local. No
Windows service is installed. Optional machine startup instead installs protected
scripts and configuration under `%PROGRAMDATA%\GamingStatusBench\TimerMachine`
and registers the named SYSTEM task `GamingStatusBench-Timer-Machine` for boot
and logon. It does not elevate the desktop account. This mode stores local
state/error logs, a pre-reboot verification baseline, and backups of replaced
startup entries. Standard users can read status but cannot modify the executable
scripts/configuration. The machine holder can continue across sign-out; disabling
the task does not stop an already running holder. Verified boot changes also
write a local pending-reboot receipt so the GUI can retain that warning.
For managed user/machine modes, GUI reboot receipts live under
`%LOCALAPPDATA%\GamingStatusBench\Workspace`, independently of timer scope.
Older timer-directory receipts are still read. These contain the action name
and boot/write timestamps; they are not uploaded.

Windows validation writes screenshots and test evidence under `reports\ui-qa`.
Treat these as private reports too; screenshots can contain machine names and
system details. They are excluded from the fixed release package file list.

## Before Sharing Reports

Review JSON/HTML reports before posting them publicly. They can contain machine
names, Windows user names, hardware identifiers, adapter names, local network
details and raw command output.

## Packaging

The release ZIP contains readable PowerShell and batch files. There is no
installer and no EXE wrapper.
