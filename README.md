# Remote File Copy (Admin Share)

Windows HTA tool that copies a local file or folder to one or more remote PCs using administrative shares (`C$`, `D$`, etc.).

**Version:** 2.5  
**File:** `RemoteCopy.hta`

## Requirements

- Windows (mshta / Internet Explorer HTA host)
- PowerShell
- Network access to target PCs
- Permission to use admin shares (File and Printer Sharing enabled; admin rights on targets)
- Username and password (required) — domain or local admin credentials for admin shares

## How to run

Double-click `RemoteCopy.hta`, or run:

```bat
mshta "C:\path\to\RemoteCopy.hta"
```

## Usage

1. **Source** — choose a local file or folder (`File...` / `Folder...`).
2. **Computers**
   - **Load PC List...** — text file, one computer name or IP per line (required)
   - Use **Select All** / **Clear All** as needed
3. **Ping Check** — marks Online/Offline on the PC list.
4. **Only copy to Online PCs** (on by default) — on **Start Copy**, auto-pings the selected PCs, copies only hosts that respond, skips offline, and aborts if none are online.
5. **Destination Path** — local path on the remote PC, e.g. `C:\Temp` or `D:\Deploy`.
   - **Custom Path** or **Computer Startup** preset  
     (`C:\ProgramData\Microsoft\Windows\Start Menu\Programs\StartUp`)
6. **Username / Password** (required) — e.g. `DOMAIN\admin` or `.\Administrator`.
7. **Recurse** — copy entire folder trees (on by default).
8. **Start Copy** — opens a PowerShell console so you can watch progress, warnings, and errors.

**Open** next to a PC name opens Explorer on that PC’s destination via the admin share.

## How it works

Destination `C:\Temp` on `PC01` becomes:

```text
\\PC01\C$\Temp
```

For each target the script:

1. Clears any old `net use` session for that share
2. Connects with the required credentials
3. Creates the destination folder if missing
4. Copies with `Copy-Item -Recurse -Force`
5. Disconnects the share session

Console stays visible so real connect/copy failures show as `[FAILED]`.

## PC list file format

```text
PC01
PC02
192.168.1.50
```

Empty lines are ignored.

## Notes

- Destination must be a drive-letter path (`C:\...`), not a UNC path.
- Username and password are required for Start Copy and Open.
- Targets need the admin share enabled and reachable (firewall / SMB).
- Single-instance HTA: only one window at a time.
