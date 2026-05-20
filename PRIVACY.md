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

The optional `NET REFRESH` solver action runs local Windows `ipconfig`
commands to release/renew DHCP and flush DNS. It does not contact the author or
upload anything, but it briefly disconnects local network connectivity while the
lease is renewed.

## Files Written

Reports are written locally under `reports\` as JSON and, unless disabled, HTML.
Solver actions only run after the user clicks an explicit button or command.

## Before Sharing Reports

Review JSON/HTML reports before posting them publicly. They can contain machine
names, Windows user names, hardware identifiers, adapter names, local network
details and raw command output.

## Packaging

The release ZIP contains readable PowerShell and batch files. There is no
installer and no EXE wrapper.
