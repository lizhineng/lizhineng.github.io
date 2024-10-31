---
layout: post
title: "EdgeOS Cheatsheet"
---

The WebUI has been disabled on my router from the start, and occasionally I
need to tinker with it via the terminal. Since these tasks aren't frequent
(thanks to the router's stability), noting down essential commands can save
time and effort in the long run. Hopefully, they will be helpful to you
as well.

## Configuration Mode

EdgeOS CLI operates in two distinct modes: **configuration** mode and
**operational** mode. To enter configuration mode, simply type
`configure`, and when you're done, use `exit` to return to operational mode.

### Connect to the Internet via PPPoE on eth0

```bash
edit interfaces ethernet eth0
set description "Internet (PPPoE)"
set pppoe 0 user-id <PPPoE Username>
set pppoe 0 password <PPPoE Password>
```

### Assign a Static IP to a Device via MAC Address

Be sure to replace the subnet CIDR with your LAN's configuration.

```bash
edit service dhcp-server shared-network-name LAN subnet 192.168.10.0/24
set static-mapping <name> ip-address <ip-address>
set static-mapping <name> mac-address <mac-address>
```

## Operational Mode

When you log in to your router, you’ll start in operational mode, identified
by the dollar sign (`$`). To switch back from configuration mode, just use
the `exit` command.

```bash
lizhineng@ubnt:~$
```

### Re-establish PPPoE Connection on pppoe0

This is handy when you want to manually reconnect to the Internet, especially
since some ISPs forcefully terminate connections every 7 days. By picking your
hours, you can avoid this happening during a critical bug fix!

```bash
disconnect interface pppoe0
connect interface pppoe0
```

### List DHCP Client Leases

You can optionally specify a pool. Without arguments, it lists clients
from all pools.

```bash
show dhcp leases
```

For clients from the LAN pool only:

```bash
show dhcp leases pool LAN
```

### List All Network Interfaces

A simplified version of `ip a`, giving a quick overview of network interfaces.

```bash
show interfaces
```

### Backup and Restore Configuration

Since the `save` command is broken in the official firmware (not a big deal,
it uses `scp` under the hood), you can still manually back up your
configuration. Don’t forget to set up SSH credentials beforehand. The backup
file will be suffixed with the date and time.

```bash
scp /config/config.boot <user>@<host>:/path/to/config.boot.$(date '+%Y%m%d%H%M%S')
```

To restore from a backup, use the `load` command:

```bash
configure
load scp://<user>@<host>/path/to/config.boot
compare
commit; save
```

### Display Hardware and Firmware Information

Displays router model, serial number, firmware version, and uptime.

```bash
show version
```

## Useful Links

- [EdgeRouter Help Center](https://help.ui.com/hc/en-us/sections/360008075214-EdgeRouter)
