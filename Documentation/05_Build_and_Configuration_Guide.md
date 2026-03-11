# 05_Build_and_Configuration_Guide

## Purpose

This document provides the step-by-step procedure to build and configure the CyberLab environment from a clean host system.

It ensures:

- Reproducibility
- Consistency
- Controlled configuration
- Verified segmentation
- Baseline validation prior to experimentation

This guide reflects the validated v1.0 baseline.

---

# 1. Host Preparation

## 1.1 Install VirtualBox

- Install Oracle VirtualBox (Stable release).
- Accept default installation settings.
- Reboot if prompted.

Verify installation:

VirtualBox launches successfully.

---

# 2. Create Windows 11 Pro VM

## 2.1 VM Creation

- Name: `WindowsMachine`
- Type: Microsoft Windows
- Version: Windows 11 (64-bit)
- Memory: Minimum 4 GB (8 GB recommended)
- CPU: Minimum 2 cores
- Disk: 60 GB dynamically allocated (minimum)

Attach Windows 11 ISO.

---

## 2.2 Install Windows

- Complete standard Windows installation.
- Create local administrative account.
- Apply initial Windows updates.
- Confirm system operational before network segmentation.

---

# 3. Create Kali Linux VM

## 3.1 VM Creation

- Name: `KaliLinuxMachine`
- Type: Linux
- Version: Debian (64-bit)
- Memory: Minimum 2 GB (4 GB recommended)
- CPU: Minimum 2 cores
- Disk: 40 GB dynamically allocated

Attach Kali ISO.

---

## 3.2 Install Kali

- Complete standard installation.
- Create non-root user (e.g., KaliUser).
- Verify login and system functionality.
- Update packages:

sudo apt update && sudo apt upgrade
[sudo] password for labuser: 

```
Get:1 http://kali.download/kali kali-rolling InRelease [34.0 kB]
Get:2 http://kali.download/kali kali-rolling/main amd64 Packages [20.7 MB]
Get:3 http://kali.download/kali kali-rolling/main amd64 Contents (deb) [52.3 MB]
Get:4 http://kali.download/kali kali-rolling/non-free amd64 Packages [184 kB]
Fetched 73.2 MB in 5s (13.5 MB/s)                               
11 packages can be upgraded. Run 'apt list --upgradable' to see them.
The following packages were automatically installed and are no longer required:
  curlftpfs                libfuse2t64             libmupdf25.1      libsphinxbase3t64   python3-aiocache        ruby-unf-ext
  libaudio2                libgav1-1               libpocketsphinx3  libswscale8         python3-aiomcache       vdpau-driver-all
  libavfilter10            libmjpegutils-2.1-0t64  libpostproc58     libvdpau-va-gl1     python3-fs
  libavformat61            libmpeg2encpp-2.1-0t64  librubberband2    mesa-vdpau-drivers  python3-wapiti-arsenic
  libconfig-inifiles-perl  libmplex2-2.1-0t64      libsnmp40t64      pocketsphinx-en-us  python3-yaswfp
Use 'sudo apt autoremove' to remove them.

Not upgrading:
  colord    libcolorhug2  libgbm1          libglx-mesa0     nodejs              python3-tables-lib
  graphviz  libegl-mesa0  libgl1-mesa-dri  mesa-libgallium  python3-pygraphviz

Summary:
  Upgrading: 0, Installing: 0, Removing: 0, Not Upgrading: 11

```

---

# 4. Configure Network Adapters

Each VM must have two adapters.

---

## 4.1 Adapter 1 – Internal Network (LabNet)

VirtualBox Settings:

- Adapter 1: Enabled
- Attached to: Internal Network
- Name: `LabNetInt`
- Promiscuous Mode: Deny
- Cable Connected: Yes

This creates the isolated internal segment.

---

## 4.2 Adapter 2 – NAT (Controlled Egress)

VirtualBox Settings:

- Adapter 2: Enabled
- Attached to: NAT
- Promiscuous Mode: Deny
- Cable Connected: Yes

This allows outbound update connectivity only.

---

# 5. Configure Static IP – Windows 11

On Windows internal adapter:

Control Panel → Network & Internet → Adapter Settings → Properties → IPv4

Set:

- IP Address: 192.168.100.10
- Subnet Mask: 255.255.255.0
- Default Gateway: Leave blank
- DNS: Leave blank for internal interface

Disable IPv6 on internal adapter.

Verify:

ipconfig
```
Windows IP Configuration


Ethernet adapter Ethernet:

   Connection-specific DNS Suffix  . :
   IPv4 Address. . . . . . . . . . . : 192.168.100.10
   Subnet Mask . . . . . . . . . . . : 255.255.255.0
   Default Gateway . . . . . . . . . :
```

Expected:
- 192.168.100.10
- No default gateway

---

# 6. Configure Static IP – Kali Linux

Identify internal interface:

ip -br addr
```
lo               UNKNOWN        127.0.0.1/8 ::1/128 
eth0             UP             192.168.100.20/24 
eth1             UP             10.0.3.15/24 fd17:625c:f037:3:87c:8ddd:258a:813c/64 fe80::5af5:f9fa:a084:a612/64 
```

Configure internal interface (example eth0):

Using NetworkManager (nmcli):

```
sudo nmcli connection modify lab_internal
ipv4.method manual
ipv4.addresses 192.168.100.20/24
ipv4.gateway ""
ipv6.method ignore
```
Bring connection up:

```
sudo nmcli connection up lab_internal
```

Verify:

ip -br addr
```
lo               UNKNOWN        127.0.0.1/8 ::1/128 
eth0             UP             192.168.100.20/24 
eth1             UP             10.0.3.15/24 fd17:625c:f037:3:87c:8ddd:258a:813c/64 fe80::5af5:f9fa:a084:a612/64 
```

ip route
```
default via 10.0.3.2 dev eth1 proto dhcp src 10.0.3.15 metric 101 
10.0.3.0/24 dev eth1 proto kernel scope link src 10.0.3.15 metric 101 
192.168.100.0/24 dev eth0 proto kernel scope link src 192.168.100.20 metric 100 
```

Expected:
- 192.168.100.20 on internal interface
- Default route via NAT interface only

---

# 7. Validate Internal Connectivity

From Kali:

ping 192.168.100.10

```
PING 192.168.100.10 (192.168.100.10) 56(84) bytes of data.
64 bytes from 192.168.100.10: icmp_seq=1 ttl=128 time=5.29 ms
64 bytes from 192.168.100.10: icmp_seq=2 ttl=128 time=2.06 ms
64 bytes from 192.168.100.10: icmp_seq=3 ttl=128 time=1.81 ms
64 bytes from 192.168.100.10: icmp_seq=4 ttl=128 time=1.23 ms
^C
--- 192.168.100.10 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3013ms
rtt min/avg/max/mdev = 1.233/2.597/5.285/1.580 ms

```


From Windows:

ping 192.168.100.20

```
Pinging 192.168.100.20 with 32 bytes of data:
Reply from 192.168.100.20: bytes=32 time<1ms TTL=64
Reply from 192.168.100.20: bytes=32 time=1ms TTL=64
Reply from 192.168.100.20: bytes=32 time=1ms TTL=64
Reply from 192.168.100.20: bytes=32 time=1ms TTL=64

Ping statistics for 192.168.100.20:
    Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),
Approximate round trip times in milli-seconds:
    Minimum = 0ms, Maximum = 1ms, Average = 0ms
```


Expected:
- Successful bidirectional ping
- Low latency
- 0% packet loss

---

# 8. Validate Segmentation

Confirm:

- Windows internal adapter has no default gateway.
- Kali default route exists only via NAT interface.
- No bridged adapters configured.
- No host LAN IP reachable from LabNet.

Check Kali routing table:

ip route
```
default via 10.0.3.2 dev eth1 proto dhcp src 10.0.3.15 metric 101 
10.0.3.0/24 dev eth1 proto kernel scope link src 10.0.3.15 metric 101 
192.168.100.0/24 dev eth0 proto kernel scope link src 192.168.100.20 metric 100 
```


---

# 9. Apply Updates via NAT

Temporarily allow updates:

Windows:
- Run Windows Update via NAT interface.

Kali:

```
sudo apt update && sudo apt upgrade
```

Confirm systems patched before baseline export.

---

# 10. Disable IPv6 on LabNet

Ensure IPv6 disabled on:

- Windows internal adapter
- Kali internal interface (via nmcli or sysctl)

Confirm IPv6 not present on 192.168.100.x interface.

---

# 11. Baseline Snapshot

Before experimentation:

1. Power off both VMs.
2. Take VirtualBox snapshots:
   - Windows: `WindowsMachine_Baseline`
   - Kali: `KaliLinuxMachine_Baseline`
3. Confirm both boot correctly after snapshot.

---

# 12. Export Baseline OVA

From VirtualBox:

File → Export Appliance

Include:
- Windows11_Lab
- Kali_Lab

Export file name format:

```
YYYY-MM-DD_CyberLab_v1.0_Baseline.ova
```

Store in:

```
/Exports/
```


---

# 13. Baseline Validation Checklist

Before marking version as validated:

- [ ] Internal ping successful
- [ ] No default gateway on Windows internal
- [ ] NAT route only on designated interface
- [ ] IPv6 disabled on LabNet
- [ ] Updates completed
- [ ] Snapshots taken
- [ ] OVA exported
- [ ] Documentation updated

Only after completion should version be recorded in:

- 06_Change_Log.md
- 07_Snapshot_and_Version_Record.md

---

# 14. Governance Reminder

This lab is:

- Isolated
- Controlled
- Non-bridged
- Non-external
- Used strictly for structured experimentation

No external testing is permitted.

---

End of Document