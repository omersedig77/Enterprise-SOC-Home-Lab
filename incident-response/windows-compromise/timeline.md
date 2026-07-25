# Windows Compromise Timeline

| Stage | Activity |
|-|-|
| Reconnaissance | Nmap scan against Windows endpoint |
| Initial Access | RDP brute force using Hydra |
| Access Obtained | Successful RDP authentication |
| Delivery | Reverse shell payload transferred |
| Execution | Reverse shell executed |
| Persistence | Registry Run Key modification |
| Privilege Escalation | Unquoted Service Path exploitation |
| Credential Access | LSASS memory dumping |
| Credential Access | Mimikatz execution |
| Exfiltration | Outbound communication observed |
