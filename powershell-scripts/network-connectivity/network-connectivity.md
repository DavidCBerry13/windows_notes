# PowerShell Network Connectivity

PowerShell contains two cmdlets that are useful for troubleshooting network connectivity issues.

- [`Test-Connection`](https://learn.microsoft.com/en-us/powershell/module/microsoft.powershell.management/test-connection) - The PowerShell version of ping.  Sends ICMP packets to the destination server and reports if a response was received.
- [`Test-NetConnection`](https://learn.microsoft.com/en-us/powershell/module/nettcpip/test-netconnection) - Attempts to connect to a specific port on a server.  Useful for testing if a server is listening on a particular port and if firewalls are allowing connectivity on that port.

## `Test-Connection`

`Test-Connection` is the equivalent to the `ping` command in PowerShell.  `Test-Connection` returns its output on the PowerShell pipeline so it can easily interact with other PowerShell cmdlets.  In PowerShell 6 and later, `Test-Connection` includes a `-Traceroute` option, giving it the ability to emulate the `tracert` command.

The `-ComputerName` paramter can accept either a hostname or an IP address

```PowerShell
Test-Connection -ComputerName www.google.com
```

```output
   Destination: www.google.com

Ping Source           Address                   Latency BufferSize Status
                                                   (ms)        (B)
---- ------           -------                   ------- ---------- ------
   1 LAPTOP-BV0RB5MQ  142.250.191.100               297         32 Success
   2 LAPTOP-BV0RB5MQ  142.250.191.100               102         32 Success
   3 LAPTOP-BV0RB5MQ  142.250.191.100               511         32 Success
   4 LAPTOP-BV0RB5MQ  142.250.191.100                81         32 Success
```

Using the `-Repeat` parameter will cause the cmdlet to continuously ping the destination

```PowerShell
Test-Connection -ComputerName www.google.com -Repeat
```

Including the `-Quiet` parameter will have the cmdlet return a simple *true* or *false* of if the machine is up and responsing.  This is especially useful when writing scripts.

```PowerShell
Test-Connection -ComputerName MyServer -Quiet
```

## `Test-NetConnection`

`Test-NetConnection` is used to check if a port is open on the server.  This is similar to trying to telnet to a port on a server to see if the port is open.  This is also useful when troubleshooting firewall issues.

```PowerShell
Test-NetConnection -ComputerName MyServer -Port 1234
```
