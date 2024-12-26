# System Information

PowerShell is able to obtain system information from Windows WMI classes using the `Get-CimInstance` cmdlet.

| **WMI Class**          | **Description**                                                                                         |
|------------------------|---------------------------------------------------------------------------------------------------------|
| Win32_OperatingSystem  | Operating system information like Windows version, install date, bootup time, memory size, OS bitness   |
| Win32_ComputerSystem   | Hardware information, domain join information                                                           |
| Win32_Processor        | Processor manufacturer, generation, clock speed, number of cores                                        |
| Win32_BIOS             | BIOS Information                                                                                        |
| Win32_SystemDriver     | Driver information                                                                                      |

Most WMI classes contain many many more properties than what display by default in the PowerShell output.  To see all of the available properties, include `-Property *` with the `Get-CimInstance` command.  For example:

```PowerShell
Get-CimInstance -Name Win32_Processor -Property *
```

## Sample Commands

### Win32_OperatingSystem

This command queries the `Win32_OperatingSystem` to get the OS Version information, when the OS was installed, and when it was last booted.

```PowerShell
Get-CimInstance -ClassName Win32_OperatingSystem | 
    Select-Object Caption,Version,InstallDate,LastBootUpTime,OsArchitecture
```

This is an example of the output:

```Output
Caption        : Microsoft Windows 10 Home
Version        : 10.0.19045
InstallDate    : 11/6/2021 6:40:17 AM
LastBootUpTime : 12/22/2024 1:24:04 PM
OsArchitecture : 64-bit
```

### Win32_ComputerSystem

This command queries the `Win32_ComputerSystem` to get if the machine is part of an Active Directory domain and the amount of RAM installed on the machine.

```PowerShell
Get-CimInstance -ClassName Win32_ComputerSystem |
    Select-Object -Property Name,PartOfDomain,Domain,Model,Manufacturer,@{Name="MemoryGB";Expression={$_.TotalPhysicalMemory/1gb -as [int]}}
```

This is an example of the output

```Output
Name         : LAPTOP-BV0RB5MQ
PartOfDomain : False
Domain       : WORKGROUP
Model        : Surface Laptop 4
Manufacturer : Microsoft Corporation
MemoryGB     : 16
```

### Win32_Processor

This command gets information about the processor on a machine

```PowerShell
Get-CimInstance –ClassName Win32_Processor |
    Select-Object -Property Manufacturer,Name,Caption,MaxClockSpeed,NumberOfCores,NumberOfLogicalProcessors
```

This is an example of the output

```Output
Manufacturer              : GenuineIntel
Name                      : 11th Gen Intel(R) Core(TM) i7-1185G7 @ 3.00GHz
Caption                   : Intel64 Family 6 Model 140 Stepping 1
MaxClockSpeed             : 2995
NumberOfCores             : 4
NumberOfLogicalProcessors : 8
```

### Win32_BIOS

What fields are populated in the Win32_Bios class varies from manufacturer to manufacturer.  There also does not seem to be a good standard of what information is put where.

This is one example query:

```PowerShell
Get-CimInstance -Classname Win32_Bios | Select-Object -Property Manufacturer,SMBIOSBIOSVersion,ReleaseDate
```