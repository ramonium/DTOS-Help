# DTOS - Prod v3.1.0

## Change Log
- Added initialization logging with optional serial scanner support and settings validation.
- Introduced IP address reachability checks and detailed network logging.
- Enhanced splash screen with initialization log display, version and customer info, color-coded log list, and Altalus branding.
- Integrated licensing API and replaced direct username retrieval.
- Implemented log archiving and backup utilities with configurable grace periods, per-file result logging, and relative path resolution.
- Expanded backup to include template and base configuration files while verifying required directories.
- Initialized log manager before archiving and improved error and path logging for reliability.
- Allowed PLC IP to be configured and added detailed PLC connection error logging.
- Added networking diagram and updated documentation.
- Added optional startup script executed before the main window; can be disabled or skipped if missing.

## Configuration
The application is configured through `DTOS.Core.exe.config`.

### Logging and Traceability
- `LogPath` (default `C:\Log\`): root folder where daily log directories (`yyyyMMdd`) are created.
- `LogLevel` (default `ERROR`): minimum severity recorded in the main log.
- `TraceabilityPath` (default `C:\Log\`): location for traceability logs.
- `TraceabilityLevel` (default `INFO`): minimum severity for traceability logging.

### Archiving Settings
- `ArchivePath` (default `C:\Log\Archive`): folder where monthly ZIP archives are stored.
- `GraceDaysAtMonthStart` (default `5`): days after the start of a month during which the previous month's logs are not archived.
- `MinDaysBeforeArchive` (default `30`): minimum age in days before a log directory is eligible for archive.

## Backup

- The backup process reads the file specified by `BackupListFile` (default `backup_list.txt`) located relative to the executable directory. Entries may be absolute or relative to the test files folder (`DTOS.Core.exe.config.TestFilesPath`).
- Each backup directory includes a `backup.log` that records every copied file with the username, timestamp, and labeled metadata `Created:` and `Last edited:` using `yyyy-MM-dd HH:mm:ss` timestamps.

### Backup Settings
- `BackupPath` (default `D:\Backup`): root directory for backup copies.
- `BackupListFile` (default `backup_list.txt`): path (relative to the executable) of the file listing additional files or directories to copy.
- `TestFilesPath` (default `D:\Conf`): base directory for configuration files.

### Files and Sequences
- `RefsDBPath` (default `Cordset_Refs2.xml`): reference database file.
- `DefaultSequence` (default `default_seq.xml`): default test sequence file.
- `Version` (Current `3.1.0`): application version identifier.

### Operational Limits and Credentials
- `MaxNbConsecutiveFailures` (default `5`): consecutive failure threshold before stopping test sequence.
- `Password` (default `admin`): default password.

### Hardware
- `FinishedGoodPrinterIP`, `ACPlugPrinterIP`: IP addresses of the finished goods and AC plug printers.
- `FinishedGoodPrinter`, `ACPlugPrinter`: enable or disable the respective printers.
- `ScannerPort` (default `COM11`): serial port for the barcode scanner (Not used in current mode).
- `BaudRate` (default `115200`): communication speed for the scanner port (Not used in current mode).
- `UseSerialScanner`: toggles serial scanner support (Not used in current mode).
- `Cameras`: collection of camera connection definitions ( Keyence ).
- `PLCIP`: IP address of the PLC.
- `LocalNetworkIP`: IP address of the local machine ( Station 1 ).
- `StartBtnAddress`: PLC address for the start button.
- `EnableShippingRobot`: enables Shipping Client control ( Station 1 ).

### Printer Connectivity
- During startup, `ValidateNetworkConnectivity()` pings the configured printers before initializing the printer manager.
- `FinishedGoodPrinter` and `ACPlugPrinter` determine whether each printer is required. When a flag is `false`, unreachable printers are logged but do not cause initialization to fail.
- `InitPrinterManger()` uses the same flags to enable only the selected printers.
- `BackupCriticalFiles()` runs regardless of printer reachability, ensuring backups are always made.

### Station and UI
- `StationIndex`: station identifier ( Station 1/2 ).
- `SetupStation`: Enables setup of references ( Station 1 ).
- `EnableYieldUI`: display the yield screen.

### ITAC Integration
- `ItacEnabled`: enable ITAC connectivity.
- `ItacId`: ITAC Id.
- `ItacLayer`: ITAC layer value.

### Manuals
- `Manual1`–`Manual7`: part numbers for manual operations (Pick-to-light controller and PN Check).

### Startup Script
- `StartupScript` (default `Startup.bat`): optional batch file executed when the application starts. The script must reside in the same directory as `dtos.core.exe` and runs hidden before the main window appears. Leave the value blank to disable execution. If the configured file is missing, the application continues without running a script. The default script maps required network drives.

## Archiving
`ArchiveLogs` scans `LogPath` for subdirectories named as dates (`yyyyMMdd`), groups them by month, and for each month creates or updates a `yyyyMM.zip` archive in `ArchivePath`. Directories from the current month are skipped, as are last month's directories during the first `GraceDaysAtMonthStart` days. Only directories older than `MinDaysBeforeArchive` days are included. Each archived directory's files are added to the ZIP and the original folder is removed.

## Backup
`BackupCriticalFiles` copies important files to `BackupPath`. Each run rewrites `BackupPath\\Last` and refreshes `BackupPath\\PreviousMonth` only if the existing backup is older than 30 days. Additional files and folders are listed in `BackupListFile`, located beside the executable. In the list, absolute entries such as `C:\\Data\\config.xml` are copied as-is, while relative entries like `configs\\settings.xml` resolve against `TestFilesPath`. Entries can also target files inside the test files path, for example `D:\\Conf\\extra\\profile.xml` when `TestFilesPath` is `D:\Conf`.

## Networking Scheme
![Network Scheme](https://github.com/ramonium/DTOS_v3/blob/master/Network%20Diagram.png) 

