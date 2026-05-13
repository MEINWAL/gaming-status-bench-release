# Gaming Status Bench v0.1.3

Third feedback-focused update. This release tightens CPU/USB solver behavior
and makes the JSON output easier to consume programmatically.

## What Changed

- Added CPU topology guidance for visible logical processors, expected logical
  threads and active `numproc` boot caps.
- Added SMT / Hyper-Threading visibility checks.
- Added safer CPU solver state:
  - `CPU SET 16` is only allowed for confirmed safe 8C/16T targets.
  - `CPU RESTORE` removes an active `numproc` cap when full thread visibility is
    wanted.
  - The report now explains that BIOS/UEFI controls SMT; `CPU SET 16` cannot
    enable SMT/Hyper-Threading.
- Improved handling for high-thread CPUs such as Ryzen 9 16-core profiles where
  `numproc 16` may be an intentional physical-core benchmark profile instead of
  an automatic must-fix.

## USB Power Saving

- Added registry fallback checks for USB/HID device power-saving flags when the
  WMI `MSPower_DeviceEnable` backend is unavailable.
- Added/updated `USB POWER SAVE OFF` solver behavior for strict input-latency
  benchmark profiles.
- USB findings now report matched registry/WMI state more clearly.

## JSON / Report Consistency

- Normalized `FixSteps` and `FixCommands` so JSON reports emit arrays even for
  zero or one item.
- This avoids mixed string/object/array shapes in downstream tools.

## Trust / Packaging

- No installer.
- No EXE wrapper.
- Plain PowerShell scripts inside the ZIP.
- SHA256 checksum provided.
- Source-available under the PolyForm Strict License 1.0.0.

## Download

Use the release asset:

```text
GamingStatusBench-v0.1.3.zip
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
