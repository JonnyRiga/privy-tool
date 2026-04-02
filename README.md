```
 ██████╗ ██████╗ ██╗██╗   ██╗██╗   ██╗
 ██╔══██╗██╔══██╗██║██║   ██║╚██╗ ██╔╝
 ██████╔╝██████╔╝██║██║   ██║ ╚████╔╝
 ██╔═══╝ ██╔══██╗██║╚██╗ ██╔╝  ╚██╔╝
 ██║     ██║  ██║██║ ╚████╔╝    ██║
 ╚═╝     ╚═╝  ╚═╝╚═╝  ╚═══╝     ╚═╝
```

# Privy — Linux Privilege Escalation Enumeration Tool

**Version:** 1.1 | **Author:** Pentest-Ready

A comprehensive bash-based local privilege escalation enumeration script for Linux. Privy runs 15 structured phases of enumeration, flags findings in real time, and generates ready-to-use exploit path suggestions with prioritised commands — no external dependencies required.

---

## Features

- **15 enumeration phases** covering every common privesc surface
- **Automatic findings detection** — flags misconfigurations and dangerous conditions as it runs
- **Prioritised exploit paths** (P1/P2/P3) with copy-paste commands generated from live scan data
- **GTFOBins integration** — cross-references SUID/SGID binaries and sudo permissions against known GTFOBins vectors
- **CVE checks** — detects Baron Samedit (CVE-2021-3156) and PwnKit (CVE-2021-4034) via version/binary analysis
- **Structured output** — all results saved to organised files under a `Privy/` directory
- **No dependencies** — pure bash, works on any standard Linux system

---

## Usage

```bash
chmod +x privy.sh
./privy.sh
```

Results are written to `./Privy/` in the current working directory. If the directory already exists it is backed up automatically before a fresh run.

---

## Enumeration Phases

| Phase | Area | Output File |
|-------|------|-------------|
| 1 | System Information (kernel, OS, env, mounts) | `SysInfo.txt` |
| 2 | User & Group Information (sudo, sudoers, UID 0) | `UserGroupInfo.txt` |
| 3 | Passwd & Shadow Files | `Passwd.txt` / `Shadow.txt` |
| 4 | Services & Running Processes | `RootServices.txt` |
| 5 | Cron Jobs & Scheduled Tasks | `CronJobs.txt` |
| 6 | PATH & Environment (LD_PRELOAD, shell profiles) | `PATH-Info.txt` |
| 7 | Network Information (interfaces, firewall, NFS) | `NetworkInfo.txt` |
| 8 | SUID / SGID Binaries & Capabilities | `SUID-GUID.txt` |
| 9 | SSH Keys & Cloud Credentials | `SSHKeys.txt` |
| 10 | MySQL / Database Info | `MySQL.txt` |
| 11 | Interesting Files & Logs | `InterestingLogs.txt` |
| 12 | File System Enumeration (backups, recent changes) | `FileSystem.txt` |
| 13 | Shell Histories | `Histories.txt` |
| 14 | Dev Tools & File Transfer Binaries | `DevTools.txt` |
| 15 | Exploit Path Suggestions (auto-generated) | `01-ExploitPaths.txt` |

---

## Output Structure

```
Privy/
├── 00-FINDINGS.txt       ← All flagged findings in one place
├── 01-ExploitPaths.txt   ← Prioritised exploit commands (P1/P2/P3)
├── SysInfo.txt
├── UserGroupInfo.txt
├── Passwd.txt
├── Shadow.txt
├── RootServices.txt
├── CronJobs.txt
├── PATH-Info.txt
├── NetworkInfo.txt
├── SUID-GUID.txt
├── SSHKeys.txt
├── MySQL.txt
├── InterestingLogs.txt
├── FileSystem.txt
├── Histories.txt
└── DevTools.txt
```

Start with `00-FINDINGS.txt` for a triage summary, then `01-ExploitPaths.txt` for actionable next steps.

---

## Exploit Path Priorities

| Priority | Meaning |
|----------|---------|
| **[P1]** | Immediate — run it now, likely instant root |
| **[P2]** | Likely root — requires one or two additional steps |
| **[P3]** | Investigate — depends on context |

P1 vectors automatically detected include: writable `/etc/passwd`, readable `/etc/shadow`, Docker socket access, lxd/lxc group membership, disk group membership, `sudo NOPASSWD: ALL`, and GTFOBins-matched SUID binaries. Each entry includes the exact command to run.

---

## Detection Coverage

**Dangerous group memberships:** `docker`, `lxd`/`lxc`, `disk`

**Sudo abuse:** NOPASSWD entries for shells, interpreters (`python`, `perl`, `ruby`, `node`), editors (`vim`, `nano`), pagers (`less`, `more`), and file manipulation tools (`cp`, `dd`, `tee`, `wget`, `curl`, `tar`, `git`, `nmap`, `find`, `awk`, `env`, `xxd`)

**SUID GTFOBins:** `bash`, `find`, `python`, `perl`, `vim`, `awk`, `env`, `tar`, `less`, `more`, `nano`, `nmap`, `pkexec` (PwnKit), `screen` (CVE screen-4.5.0), `cp`, and 50+ more

**Capabilities:** `cap_setuid`, `cap_setgid`, `cap_dac_override`, `cap_sys_admin`, `cap_sys_ptrace`, `cap_net_raw`, `cap_fowner`

**Cron & service hijacks:** Writable cron scripts, writable systemd service files, writable `/etc/crontab`

**Credential exposure:** Readable `/etc/shadow`, readable SSH private keys, `.netrc` files, AWS/GCloud/Azure credential files, MySQL root no-password access, readable backup files in `/var/backups/`

**Container escapes:** Docker socket, lxd/lxc group, `/.dockerenv` detection

**CVEs:** Baron Samedit (CVE-2021-3156), PwnKit (CVE-2021-4034)

**Other vectors:** NFS `no_root_squash`, multiple UID 0 accounts, writable `/etc/passwd`, writable PATH directories, writable LD config files, readable `/root` directory, active tmux sessions, world-writable files in `/etc/`

---

## Requirements

- Bash 4+
- Standard Linux utilities (`find`, `ps`, `id`, `ss`/`netstat`, `getcap`, etc.)
- No root required — runs as the current user and enumerates what is accessible

---

## Disclaimer

This tool is intended for authorised penetration testing, CTF competitions, and security research only. Do not use against systems you do not have explicit permission to test.
