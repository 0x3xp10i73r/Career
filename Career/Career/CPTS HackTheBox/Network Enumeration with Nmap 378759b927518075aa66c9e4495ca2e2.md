# Network Enumeration with Nmap

## Introduction to NMAP

- Network Mapper (Nmap) is an open-source network analysis and security auditing tool written in C, C++, Python, and Lua.

#### What can Nmap do ?

- Host discovery
- Port scanning
- Service enumeration and detection
- OS detection
- Scriptable interaction with the target service (Nmap Scripting Engine)

## Scan Techniques

```xml
<SNIP>
SCAN TECHNIQUES:
  -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
  -sU: UDP Scan
  -sN/sF/sX: TCP Null, FIN, and Xmas scans
  --scanflags <flags>: Customize TCP scan flags
  -sI <zombie host[:probeport]>: Idle scan
  -sY/sZ: SCTP INIT/COOKIE-ECHO scans
  -sO: IP protocol scan
  -b <FTP relay host>: FTP bounce scan
<SNIP>
flags  -sI <zombie host[:probeport]>: Idle scan  -sY/sZ: SCTP INIT/COOKIE-ECHO scans  -sO: IP protocol scan  -b <FTP relay host>: FTP bounce scan<SNIP>
```

## Host Discovery

```bash
# Scan all hosts in the subnet
sudo nmap 10.129.2.0/24 -sn -oA tnet | grep for | cut -d " " -f5

# Read targets from file
sudo nmap -sn -oA tnet -iL hosts.lst | grep for | cut -d " " -f5

# Scan selected hosts
sudo nmap -sn -oA tnet 10.129.2.18 10.129.2.19 10.129.2.20

# Scan consecutive hosts
sudo nmap -sn -oA tnet 10.129.2.18-20

# Scan single host
sudo nmap 10.129.2.18 -sn -oA host

# ICMP discovery
sudo nmap 10.129.2.18 -sn -PE

# Show packet trace
sudo nmap 10.129.2.18 -sn --packet-trace

# Show discovery reason
sudo nmap 10.129.2.18 -sn --reason

# Disable ARP and force ICMP
sudo nmap 10.129.2.18 -sn -PE --disable-arp-ping

-PE                  # ICMP Echo Request
-sn                  # Host Discovery Only
--packet-trace       # View packets
--reason             # Why host is alive/down
--disable-arp-ping   # Disable ARP discovery
-iL                  # Read targets from file
-oA                  # Save all output formats
```

## **Host and Port Scanning**

| State | Description |
| --- | --- |
| open | This indicates that the connection to the scanned port has been established. These connections can be TCP connections, UDP datagrams, as well as SCTP associations. |
| closed | When the port is shown as closed, the TCP protocol indicates that the packet received contains an RST flag. This scanning method can also be used to determine if the target is alive. |
| filtered | Nmap cannot correctly identify whether the scanned port is open or closed because either no response is returned from the target or an error code is received. |
| unfiltered | This state only occurs during a TCP ACK scan and means that the port is accessible, but it cannot be determined whether it is open or closed. |
| open|filtered | If no response is received for a specific port, Nmap sets it to this state. This indicates that a firewall or packet filter may be protecting the port. |
| closed|filtered | This state only occurs in IP ID idle scans and indicates that it was impossible to determine whether the scanned port is closed or filtered by a firewall. |

```bash
# Scan top 10 most common TCP ports
nmap <target> --top-ports=10

# Scan top 100 ports (fast scan)
nmap <target> -F

# Scan specific ports
nmap <target> -p 22,80,443

# Scan a range of ports
nmap <target> -p 22-445

# Scan all 65535 ports
nmap <target> -p-

# Disable host discovery (assume host is alive)
nmap <target> -Pn

# Disable DNS resolution
nmap <target> -n

# Disable ARP ping
nmap <target> --disable-arp-ping

# Show all packets sent and received
nmap <target> --packet-trace

# Show why Nmap assigned a state
nmap <target> --reason

# SYN/ACK      = Open Port
# RST/ACK      = Closed Port
# No Response  = Filtered/Open|Filtered
# ICMP 3/3     = UDP Closed
# -sS          = Stealthier
# -sT          = More Accurate
# -sU          = Slowest Scan
# -sV          = Service Enumeration
# -Pn          = Skip Host Discovery
# -n           = Disable DNS Resolution
# -p-          = Scan All Ports
# --reason     = Show Port State Reason
# --packet-trace = Show Raw Packets
```

## **Saving the Results**

```bash
Normal output (-oN) with the .nmap file extension
Grepable output (-oG) with the .gnmap file extension
XML output (-oX) with the .xml file extension
```

```bash
xsltproc target.xml -o target.html
```

## **Service Enumeration**

```bash
# Service Enumeration
sudo nmap 10.129.2.28 -p- -sV

# Displays scan's status every 5 seconds
--stats-every=5s

# Displays verbose output during the scan.
-v/-vv

# Sets the number of packets that will be sent simultaneously.
--min-rate 300

```