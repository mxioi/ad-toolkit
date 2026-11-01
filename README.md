# AD Toolkit

![PowerShell 5.1+](https://img.shields.io/badge/PowerShell-5.1%2B-5391FE?logo=powershell&logoColor=white)
![Active Directory](https://img.shields.io/badge/Active%20Directory-RSAT-0078D4)
![Single file](https://img.shields.io/badge/install-single%20file-2EA44F)

An interactive PowerShell menu for the Active Directory jobs that come up every day on a helpdesk or
sysadmin desk: find a user, see when they last signed in, unlock them, reset a password, chase stale
computers, and pull a quick report.

It works out your domain and domain controllers on its own, so there is nothing to configure for your
network. Copy one file and run it.

The main menu (condensed into two columns here):

```text
================================================================
              AD Toolkit v3.0 - Main Menu
================================================================

USER MANAGEMENT                 REPORTING
  1.  Search Users                9.  Locked Out Users Report
  2.  Get User Details            10. Password Expiry Report
  3.  Enable/Disable User         11. Inactive Users Report
  4.  Unlock User Account         12. Privileged Users Report
  5.  Reset User Password         13. Stale Computers Report
                                  14. AD Health Report
COMPUTER MANAGEMENT
  6.  Search Computers          BULK OPERATIONS
  7.  Get Computer Details        15. Bulk User Operations
  8.  Test Computer Connectivity  16. Import from CSV
```

## What it does

| Area | Options | Changes AD? |
|---|---|---|
| Users | Search, full details, true last logon (checked on every DC, not the replicated estimate) | No |
| Users | Enable / disable, unlock, reset password (random or typed, optional change at next logon) | **Yes** |
| Computers | Search, details (OS, uptime, signed-in user, true last logon), connectivity test | No |
| Reports | Locked out, password expiry, inactive users (90 days by default), privileged group members (Domain, Enterprise and Schema Admins, Account Operators), stale computers, AD health (DCs online, FSMO roles, account counts) | No |
| Export | Any report to CSV or HTML | No |
| Bulk | Enable, disable, unlock or reset many users from a list or a CSV | **Yes** |

Every action is written to a timestamped log, so you can see afterwards exactly what was changed.

## Requirements

- Windows PowerShell 5.1 or PowerShell 7 on Windows
- The Active Directory module (RSAT: *Active Directory Domain Services and Lightweight Directory Tools*)
- A domain-joined machine, signed in as an account with the rights for what you want to do.
  Reports only need read access; unlocks and resets need the matching delegated permissions.

## Quick start

```powershell
git clone https://github.com/mxioi/ad-toolkit
cd ad-toolkit
.\ADToolkit.ps1
```

If scripts are blocked, allow them for this session only:

```powershell
Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass
```

`-SkipInitCheck` skips the domain connectivity check on the welcome screen.

## Bulk CSV format

```csv
Username,Operation,NewPassword
jdoe,Enable
asmith,ResetPassword,
bwilson,Unlock
```

`Operation` is one of `Enable`, `Disable`, `Unlock` or `ResetPassword`. Leave `NewPassword` empty to
have one generated. Try a bulk run on a test account first.

## Where files go

| What | Where |
|---|---|
| Log (one per day) | `%TEMP%\ADTool_yyyyMMdd.log` |
| CSV and HTML exports | `Documents\ADToolExports` (change it in Settings) |

## Also in this repo

| File | What it is |
|---|---|
| `Get-UserLastLogon.ps1` | The tiny script this started as: type part of a name, pick the user, see their last logon. |
| `archive/` | Earlier versions of the toolkit, kept for reference. Use `ADToolkit.ps1`. |

## Why I built it

Most AD tickets are the same five lookups. Clicking through Active Directory Users and Computers for
each one is slow, and the "Last Logon" it shows depends on which DC you ask. This puts the common jobs
one keypress away and asks every DC for the real answer.
