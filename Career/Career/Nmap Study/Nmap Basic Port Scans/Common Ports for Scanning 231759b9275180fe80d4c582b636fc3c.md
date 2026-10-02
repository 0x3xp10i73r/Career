# Common Ports for Scanning

# **🔢 Common TCP and UDP Ports**

## **📘 Well-Known Ports (0–1023)**

| **Port** | **Protocol** | **Service** | **Description** |
| --- | --- | --- | --- |
| 20 | TCP | FTP (Data) | File Transfer Protocol (data transfer) |
| 21 | TCP | FTP (Control) | FTP login and commands |
| 22 | TCP | SSH | Secure Shell (remote login) |
| 23 | TCP | Telnet | Remote login (insecure) |
| 25 | TCP | SMTP | Email sending |
| 53 | UDP/TCP | DNS | Domain Name System |
| 67 | UDP | DHCP (Server) | Dynamic Host Configuration |
| 68 | UDP | DHCP (Client) | Dynamic Host Configuration |
| 69 | UDP | TFTP | Trivial FTP (insecure) |
| 80 | TCP | HTTP | Web traffic |
| 110 | TCP | POP3 | Email retrieval |
| 111 | TCP/UDP | RPCBind / portmapper | Remote procedure call service |
| 123 | UDP | NTP | Network Time Protocol |
| 135 | TCP/UDP | Microsoft RPC | DCOM services |
| 137 | UDP | NetBIOS Name Service | Windows name resolution |
| 138 | UDP | NetBIOS Datagram | Windows network discovery |
| 139 | TCP | NetBIOS Session | Windows file/printer sharing |
| 143 | TCP | IMAP | Email retrieval |
| 161 | UDP | SNMP | Network monitoring |
| 162 | UDP | SNMP Trap | SNMP traps |
| 389 | TCP/UDP | LDAP | Directory services |
| 443 | TCP | HTTPS | Secure web traffic |
| 445 | TCP | SMB | Windows file sharing |
| 465 | TCP | SMTPS | SMTP over SSL |
| 514 | UDP | Syslog | Log message transport |
| 515 | TCP | LPD | Line Printer Daemon |
| 587 | TCP | SMTP (Submission) | Modern SMTP |
| 631 | TCP/UDP | IPP | Internet Printing Protocol |
| 993 | TCP | IMAPS | Secure IMAP |
| 995 | TCP | POP3S | Secure POP3 |

---

## **📘 Registered Ports (1024–49151) — Common Examples**

| **Port** | **Protocol** | **Service** |
| --- | --- | --- |
| 1433 | TCP | MS SQL Server |
| 1434 | UDP | MS SQL Server |
| 1521 | TCP | Oracle DB |
| 1723 | TCP | PPTP VPN |
| 2049 | TCP/UDP | NFS |
| 2181 | TCP | Apache ZooKeeper |
| 2375 | TCP | Docker REST API |
| 3306 | TCP | MySQL |
| 3389 | TCP | RDP (Remote Desktop) |
| 3690 | TCP | SVN |
| 4444 | TCP | Metasploit handler |
| 5432 | TCP | PostgreSQL |
| 5900 | TCP | VNC (Virtual Desktop) |
| 5985 | TCP | WinRM HTTP |
| 5986 | TCP | WinRM HTTPS |
| 6379 | TCP | Redis |
| 8000 | TCP | Common web dev port |
| 8080 | TCP | HTTP Alternate |
| 8443 | TCP | HTTPS Alternate |
| 9001 | TCP | Tor ORPort |
| 9200 | TCP | Elasticsearch |

---

## **🧠 Bonus: Useful Nmap Option**

You can run:

```
nmap --top-ports 1000
```

To scan the 1,000 most commonly used ports, based on frequency.

---

Let me know if you want a printable cheat sheet PDF or categorized by service (e.g., databases, mail, web, remote access).