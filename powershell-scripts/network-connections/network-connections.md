# Netowk Connections

The PowerShell [`Get-NetTcpConnection`](https://learn.microsoft.com/en-us/powershell/module/nettcpip/get-nettcpconnection) shows information about inbound and outbound TCP connections on a machine.  This is useful to to understand what processes are listening on a machine and what processes are making outbound connections.  This cmdlet is roughly equivalent to the `netstat` command in traditional Windows and Linux OS's.

The `Get-NetTcpConnection` retuns a field called `OwningProcess` that is the process id of the process that opened the connection.  This Process ID can then used to look up the process using the `Get-Process` cmdlet to get information like the process name, process user, and start time.

 **IMPORTANT:** To get information about what user owns a process, you must:
  
- Run `Get-Process` with the `-IncludeUsername` parameter
- Run the command in an elevated (administrator) session

If you do not require user info, then you can remove this flag and run the command from a normal command prompt

## Show listening ports and processes on a machine

Listening processes and ports can be found by calling `Get-NetTcpConnection` with the `-State Listen` parameter.

```PowerShell
Get-NetTcpConnection -State Listen |
    Sort-Object -Property OwningProcess |
    Foreach-Object {        
        $ProcessInfo = Get-Process -Id $_.OwningProcess -IncludeUsername

        Write-Output $([PSCustomObject]@{
            LocalAddress      = $_.LocalAddress
            LocalPort          = $_.LocalPort
            ProcessId          = $_.OwningProcess
            ProcessName        = $ProcessInfo.Name
            ProcessUser        = $ProcessInfo.UserName
            ProcessStartTime   = $Processinfo.StartTime        
            CommandLine        = $Processinfo.CommandLine
            Description = $ProcessInfo.Description
        })
    } |
    Format-Table -Autosize    
```

The following command shows a process centric view of the same information

```PowerShell
Get-NetTcpConnection -State Listen |
    Select-Object -Property OwningProcess -Unique |
    Sort-Object -Property OwningProcess |
    Foreach-Object {
        $ProcessConnections = Get-NetTCPConnection -OwningProcess $_.OwningProcess |
            Select-Object -Property LocalAddress,LocalPort -Unique |
            Sort-Object -Property LocalAddress,LocalPort
        $ProcessInfo = Get-Process -Id $_.OwningProcess -IncludeUsername

        Write-Output $([PSCustomObject]@{
            ProcessId          = $_.OwningProcess
            ProcessName        = $ProcessInfo.Name
            ProcessUser        = $ProcessInfo.UserName
            ProcessStartTime   = $Processinfo.StartTime
            LocalAddresses     = $ProcessConnections
            CommandLine        = $Processinfo.CommandLine
            Description = $ProcessInfo.Description
        })        
    }
```

## Outbound Connections

Outbound connections can be found by calling `Get-NetTcpConnection` with the `-State Established` parameter.

This gets all of the outbound connections and what remote IP address/port they are connected to

```PowerShell
Get-NetTcpConnection -State Established |
    Sort-Object -Property OwningProcess |
    Foreach-Object {        
        $ProcessInfo = Get-Process -Id $_.OwningProcess -IncludeUsername

        Write-Output $([PSCustomObject]@{
            LocalAddress      = $_.LocalAddress
            LocalPort          = $_.LocalPort
            RemoteAddress      = $_.RemoteAddress
            RemotePort         = $_.RemotePort
            ProcessId          = $_.OwningProcess
            ProcessName        = $ProcessInfo.Name
            ProcessUser        = $ProcessInfo.UserName
            ProcessStartTime   = $Processinfo.StartTime        
            CommandLine        = $Processinfo.CommandLine
            Description = $ProcessInfo.Description
        })
    } |
    Format-Table -Autosize
```
