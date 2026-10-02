# SSRF Attacks Against Other Back-End Systems

## Target: Internal Back-End Systems

- Application servers often interact with back-end systems that are not directly accessible to external users
- These systems typically use private, non-routable IP addresses (e.g., 192.168.x.x, 10.x.x.x, 172.16.x.x ranges)
- Network topology provides the primary security layer for these systems

## Security Implications

- Back-end systems frequently maintain weaker security postures due to their assumed network isolation
- Internal services often contain sensitive functionality accessible with minimal or no authentication
- SSRF vulnerabilities effectively bypass network segmentation controls

## Example Attack Vector

Continuing from the stock application example, assume an administrative interface exists internally at:
`https://192.168.0.68/admin`

This interface is not directly accessible from external networks but can be reached via SSRF:

```
POST /product/stock HTTP/1.0
Content-Type: application/x-www-form-urlencoded
Content-Length: 118

stockApi=http://192.168.0.68/admin

```

## Risk Analysis

1. **Network Segmentation Bypass**: SSRF circumvents network-level protections designed to isolate internal systems
2. **Inadequate Internal Security**: Internal services often lack robust security controls, assuming protection via network architecture
3. **Privilege Escalation**: Direct access to administrative interfaces without proper authentication mechanisms

## Common Internal Targets

- Administrative interfaces and control panels
- Configuration management systems
- Database management interfaces (phpMyAdmin, MongoDB UI)
- Cloud provider metadata services (AWS IMDS, Azure Instance Metadata)
- Continuous integration/deployment systems (Jenkins, GitLab)
- Monitoring and logging systems (Prometheus, Grafana, Kibana)
- Internal APIs and microservices

## Attack Methodologies

1. **Internal Network Enumeration**: Systematic probing of internal IP ranges and port scanning via SSRF
2. **Service Identification**: Fingerprinting discovered services based on response characteristics
3. **Protocol Exploitation**: Leveraging support for various protocols (HTTP, HTTPS, FTP, file, gopher, etc.)
4. **Parameter Manipulation**: Modifying request parameters to exploit internal service vulnerabilities

## Defense Considerations

- Implement strict allowlisting for outbound requests from application servers
- Validate and sanitize all user-supplied URLs before processing
- Disable unnecessary URL schemes and protocols
- Apply network-level restrictions on server outbound connectivity
- Implement authentication and authorization for all internal services
- Monitor for anomalous request patterns from application servers