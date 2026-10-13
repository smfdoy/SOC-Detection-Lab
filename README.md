# SOC Detection Lab: Wazuh SIEM + Kali Linux

Entry-level SOC home lab. I attack a lab endpoint from Kali Linux, detect the activity in Wazuh, triage the alerts, and write incident reports mapped to MITRE ATT&CK.

## Goal
Show the full SOC loop: attack, detect, investigate, document.

## Lab Design
| Machine | Role | OS | RAM |
|---|---|---|---|
| Wazuh server | SIEM (manager, indexer, dashboard) | Ubuntu Server 22.04 | 4 GB |
| Endpoint | Monitored victim with Wazuh agent | Ubuntu Server or Desktop | 2 GB |
| Kali | Attacker | Kali Linux | 2 GB |

Each VM has two adapters: NAT (updates and installs) and Host-only (all lab traffic). Attacks only ever target the lab endpoint.

## Scenarios
| # | Attack (from Kali) | What I look for in Wazuh | MITRE ATT&CK |
|---|---|---|---|
| 1 | SSH brute force with Hydra | Authentication failure and brute force alerts | T1110 Brute Force |
| 2 | Nmap port scan | Network evidence (log review, optional Suricata) | T1046 Network Service Discovery |
| 3 | New user created after "access" | New user and group change alerts | T1136.001 Create Account |
| 4 | File change in a monitored folder | File integrity monitoring alert | T1565.001 Stored Data Manipulation |
| 5 | Sudo use by a low-privilege user | Sudo command alerts | T1548.003 Sudo and Sudo Caching |

## New to this? Start here
[Beginner step-by-step guide](docs/step-by-step-guide.md)

## Phases (follow in order)
- [ ] [01 Setup VMs and Network](checklists/01-setup.md)
- [ ] [02 Install Wazuh Server](checklists/02-wazuh-server.md)
- [ ] [03 Deploy Agent on Endpoint](checklists/03-agent.md)
- [ ] [04 Run Attack Scenarios](checklists/04-attacks.md)
- [ ] [05 Detect and Triage](checklists/05-triage.md)

## Repo Structure
```
soc-detection-lab/
  README.md
  checklists/   step by step checklists
  configs/      sanitized config snippets (no passwords)
  docs/         detection log, incident reports
  evidence/     screenshots
```

## Results
Detected 4 of 5 simulated attacks (SSH brute force, new account creation, file integrity change, and sudo use), all correctly flagged as true positives by Wazuh’s default ruleset. The port scan was a missed detection, which highlighted a real gap between host-based and network-based monitoring, documented in the detection log with a recommended fix (Suricata)

## What I Learned
- Wazuh’s out-of-box rules caught most common attack patterns without custom tuning
- Host-based agents have blind spots, network-level attacks like port scans need a complementary tool
- Alert severity levels map to real risk, persistence (new account) was rated higher than a single failed login, which matches how a SOC would prioritize
- Writing incident reports clarified the difference between detecting something and fully explaining its impact and response

## Disclaimer
All activity was performed on systems I own, inside an isolated lab network.
