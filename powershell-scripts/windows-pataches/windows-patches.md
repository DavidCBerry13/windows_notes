# Windows Patches

The [`Get-HotFix`](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/get-hotfix) in PowerShell provides information on what patches have been deployed to a system.  This cmdlet uses the `Win32_QuickFixEngineering` WMI class under the hood.  Calling `Get-Hotfix` is effectively the same as calling `Get-CimInstance -Name Win32_QuickFixEngineering`.

This shows the patches installed sorted by the installation date.

```PowerShell
Get-HotFix | Sort-Object -Property InstalledOn
```

To find if a specific patch is installed, use the `-Id` parameter.  The `-ErrorAction SilentlyContinue` instructs PowerShell to continue and return null if the patch is not installed.

```PowerShell
get-Hotfix -Id KB5041579 -ErrorAction SilentlyContinue 
```

## Analyzing Patches in the Event Log

When a system is patched, entries are made in the `System` event log.

- Analyzing these events is useful to get the specific times a patch started and finished installing.
- Some patches require a reboot to be completely installed.  Analyzing the event log can show if/when the reboot has happened.
- In some cases, the reboot causes problems (as in the system does not reboot successfully).  The event log can help track down these types of issues.

Events can be viewed in the Windows Event Viewer or via PowerShell

### Viewing Events in PowerShell

The [`Get-WinEvent`](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.diagnostics/get-winevent) cmdlet can be used to view Event Viewer events from PowerShell.

- Windows Update events have a provider name of `Microsoft-Windows-WindowsUpdateClient`.
- Within the `Microsoft-Windows-WindowsUpdateClient`, Event IDs are as follows:
  - *19* - Update successfully installed
  - *20* - Update failed to install
  - *43* - Windows has started to install an update
  - *44* - Windows started downloading an update

The following PowerShell lists all of the Windows Update related events from a system.

```PowerShell
Get-WinEvent -LogName SYSTEM |
    Where-Object { $_.ProviderName -eq 'Microsoft-Windows-WindowsUpdateClient' } |
    Select-Object -Property TimeCreated,ProviderName,Id,LevelDisplayName,Message |
    Sort-Object -Property TimeCreated |
    Format-Table -Autosize
```
