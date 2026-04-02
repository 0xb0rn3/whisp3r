# whisp3r Framework
**Authorized Offensive Security & Red Team Workflow Toolkit**

> 🔒 **Scoped. Authorized. Workflow-Driven.**  
> 🔧 Dev: `oxbv1 | 0xb0rn3` | 🌐 `www.oxborn3.com` | 📧 `contact@oxborn3.com`  
> 📦 Repo: `github.com/0xb0rn3/whisp3r`

---

## 📖 Overview
`whisp3r` is a dual-version offensive security framework engineered for authorized red team engagements, penetration testing, and defensive validation. Both executables are self-contained, require no `.py` extension, and share a unified architecture while serving distinct operational tiers. 

Operators select the version that matches their engagement complexity, resource constraints, and reporting requirements. All modules enforce explicit authorization, scope validation, audit logging, and dry-run validation out-of-the-box.

---

## 🔍 Version Selection Matrix

| Feature Category | `whisp3r-v1` (Stable / Core) | `whisp3r-v2` (Advanced / Full-Stack) |
|------------------|------------------------------|--------------------------------------|
| **Primary Use Case** | Quick assessments, wireless pentests, lightweight network scans | Complex red teams, full attack-chain simulation, enterprise reporting |
| **Wireless Recon** | ✅ Interface detection, monitor toggle, `airodump-ng` capture, handshake verification/cracking | ✅ + PMKID/WPS targeting, adaptive channel hopping, client isolation checks |
| **Network Discovery** | ✅ Nmap XML parsing, port enumeration, basic OS/banner extraction | ✅ + Async concurrent scanning, service version correlation, priority scoring |
| **MITM Engine** | ❌ Not included | ✅ ARP/DNS spoofing, SSL stripping, Responder credential parsing, auto-cleanup |
| **Threat Intelligence** | ✅ Local cached CVE database, static reliability mapping | ✅ Live EPSS sync, CISA KEV cross-ref, SearchSploit PoC validation, dynamic scoring |
| **Payload Generation** | ✅ `msfvenom` orchestration, basic platform/arch matching | ✅ AMSI/ETW bypass templates, multi-stage encoding, BOF generation, raw shellcode injection, post-build verification |
| **OPSEC & Evasion** | ✅ Dry-run, scope validation, process cleanup | ✅ + Jitter/rate-limiting, traffic shaping, command obfuscation, fallback routing |
| **Reporting & Integration** | ✅ JSON/CSV exports, basic finding metadata | ✅ Executive HTML reports, SIEM/SOAR webhooks, OSSTMM/NIST schema alignment |
| **Architecture** | ✅ Synchronous workflow, CLI menu, audit logging | ✅ + Async task queue, plugin loader, session state persistence, config-driven engine |
| **Dependencies** | Lightweight (`nmap`, `aircrack-ng`, `msfvenom`) | Full-stack (+ `bettercap`, `tcpdump`, `hcxtools`, `reaver`, `Responder`) |

> 💡 **Rule of Thumb**: Use `v1` for focused wireless/network assessments or constrained environments. Use `v2` for advanced red teaming, MITM simulations, evasion-capable payloads, and client-ready reporting.

---

## 🛠 Installation & Setup
Both versions are standalone Python executables with `#!/usr/bin/env python3` shebangs. No virtual environment or pip dependencies required for core functionality.

```bash
# 1. Clone or download both versions
git clone https://github.com/0xb0rn3/whisp3r.git
cd whisp3r

# 2. Make both executables
chmod +x whisp3r-v1 whisp3r-v2

# 3. Verify execution
./whisp3r-v1 --help
./whisp3r-v2 --help
```

### System Requirements
- Linux (Kali, Ubuntu, Debian, Parrot recommended)
- Python 3.10+
- Root/sudo privileges (required for raw packet operations, monitor mode, MITM)

### Tool Dependencies (Auto-Verified at Launch)
| Tool | Required By | Fallback/Note |
|------|-------------|---------------|
| `iw`, `airmon-ng`, `airodump-ng`, `aireplay-ng`, `aircrack-ng` | Wireless modules (v1 & v2) | Core recon requires full suite |
| `nmap` | Network scanning | Fails gracefully if missing |
| `msfvenom` | Payload generation | Must be in `$PATH` |
| `bettercap`, `arpspoof`, `tcpdump`, `hcxtools`, `reaver`, `Responder` | MITM & advanced recon | v2 only; v1 skips these modules |

---

## 🔄 Unified Workflow Architecture
Both versions operate on a **3-stage pipeline** that eliminates manual handoffs:

1. **Discovery & Enumeration**  
   Wireless AP capture ↔ Network host/service scanning → Structured `Host`/`AP` objects stored in memory.
2. **Context-Aware Analysis** *(v2 enhanced)*  
   Service banners matched against CVE database → EPSS/KEV/PoC scoring → Reliability-ranked vulnerability list.
3. **Exploit & Payload Delivery**  
   Automated payload generation → Verification → Metadata export → (v2) SIEM webhook dispatch.

---

## 🚀 Usage & CLI Reference
### Core Flags (Both Versions)
| Flag | Description |
|------|-------------|
| `--authorized` | Confirms written RoE (required for offensive ops) |
| `--dry-run` | Prints commands without execution |
| `-i, --interface` | Target wireless/network interface |
| `--mode` | `menu`, `wifi`, `net`, `mitm`, `cve`, `payload`, `integrated` |
| `--scope` | Path to JSON RoE scope file |
| `--target` | IP, subnet, or BSSID for direct mode |
| `--cve` | CVE ID for direct payload generation |
| `--lhost / --lport` | Callback IP & port |
| `--export` | Output format: `json` or `csv` |

### Quick Start Examples
```bash
# v1: Lightweight wireless recon & handshake cracking
sudo ./whisp3r-v1 --authorized -i wlan0 --mode menu

# v1: Direct network scan + basic payload gen
sudo ./whisp3r-v1 --authorized -i eth0 --mode net --target 10.10.10.0/24

# v2: Full MITM simulation with DNS spoof & SSL strip
sudo ./whisp3r-v2 --authorized -i eth0 --mode mitm --target 10.10.10.50

# v2: Auto-prioritized integrated workflow (Recon → Intel → Payload → Report)
sudo ./whisp3r-v2 --authorized -i eth0 --mode integrated --target 192.168.1.0/24
```

---

## ⚙️ Configuration & Scope Enforcement
### RoE Scope Validation (`scope_roe.json`)
```json
{
  "targets": ["10.10.10.0/24", "192.168.50.10"],
  "excluded": ["192.168.50.200", "10.10.10.254"],
  "rules": {
    "max_deauth_count": 500,
    "max_deauth_duration": 30,
    "allowed_payload_formats": ["exe", "elf", "bof"]
  }
}
```
Usage: `sudo ./whisp3r-v2 --authorized --scope scope_roe.json -i eth0`

### OPSEC & Engine Settings (`~/.whisp3r/whisp3r_config.json`)
```json
{
  "opsec": { "jitter": true, "jitter_ms": 75, "rate_limit_ms": 150 },
  "logging": { "level": "INFO", "rotate_mb": 50 },
  "plugins": { "auto_load": true },
  "reporting": { "siem_webhook": "", "format": "json" }
}
```

---

## 🧠 Threat Intelligence & CVE Engine (`v2`)
- **EPSS API Sync**: Fetches 30-day exploit probability scores from FIRST.org
- **CISA KEV Cross-Reference**: Flags vulnerabilities actively exploited in the wild
- **SearchSploit PoC Validation**: Programmatically verifies public exploit code existence
- **Dynamic Reliability Scoring**:  
  `Score = (EPSS × 0.4) + (PoC × 0.3) + (KEV × 0.2) + (Community × 0.1)`
- **Auto-Prioritization**: CVEs ranked by real-world exploit likelihood, not just CVSS severity.

---

## 🕵️ MITM & Credential Harvesting (`v2`)
- **Primary Engine**: `bettercap` with modular command chaining
- **Fallback**: `arpspoof` + `tcpdump` when `bettercap` is absent
- **Capabilities**: Bidirectional ARP spoofing, DNS hijacking, SSL/TLS stripping, full PCAP capture, automated credential parsing (`NTLM`, `HTTP Basic`, `Kerberos`)
- **Safety**: Automatic route restoration, IP forwarding reset, process cleanup on exit/interrupt.

---

## 💣 Payload Generation & Evasion
### `v1` (Core)
- Platform/architecture auto-inference from CVE metadata
- Standard `msfvenom` orchestration
- Post-build file verification & SHA-256 hashing

### `v2` (Advanced)
- **Evasion Options**: AMSI bypass template injection, ETW event suppression, multi-stage encoding
- **Execution Models**: Beacon Object File (`.o`) generation, raw shellcode injection
- **Verification**: Size validation, format integrity check, hash generation, metadata tagging

---

## 📊 Reporting & Interoperability
All outputs export to `./whisp3r_output/`:
- `wireless_aps.json` / `scan_<IP>.json` / `session_meta.json`
- `executive_report.html` (v2: Findings summary, risk matrix, remediation guidance)
- CSV exports for SIEM ingestion
- SIEM/SOAR webhook delivery (v2: configured in `whisp3r_config.json`)
- Schema aligned with OSSTMM, NIST SP 800-115, and CISA KEV field definitions.

---

## 🛡 Safety, Compliance & Auditing
- `--authorized` flag required for all disruptive operations
- Scope IP validation blocks out-of-bounds targeting
- `whisp3r_audit.log` records: timestamp, action, target, parameters, result, operator, authorization state
- Dry-run mode for command validation before execution
- Automatic process cleanup on `SIGINT`/`SIGTERM`/exit
- Rate limiting & jitter (v2) to reduce IDS/IPS detection probability

---

## 🔌 Plugin Architecture (`v2`)
Drop Python modules into `plugins/` with the following structure:
```python
# plugins/my_recon.py
__plugin__ = {"name": "My Recon", "version": "1.0", "entry_point": "run", "dependencies": ["scapy"]}
def run(core, args):
    core.audit.log("plugin_run", "my_recon", args, "success", core.authorized)
    # custom logic
```
Plugins inherit core scope/OPSEC settings and can extend menu options or workflow stages.

---

## 🐛 Troubleshooting & Logging
- Audit log: `~/.whisp3r/whisp3r_audit.log`
- Output dir: `./whisp3r_output/`
- Missing tools trigger graceful fallbacks (documented in CLI warnings)
- Run with `--dry-run` to validate tool paths and command syntax
- Ensure `sysctl net.ipv4.ip_forward=1` is permitted by RoE for MITM
- PCAP files may require `capinfos` or Wireshark for deep analysis

---

## 🧑‍💻 Developer & Contact
- **Aliases**: `oxbv1 | 0xb0rn3`
- **Email**: `contact@oxborn3.com`
- **Web**: `www.oxborn3.com`
- **Repository**: `github.com/0xb0rn3/whisp3r`
- **License**: MIT (Educational/Authorized Use Only)
- **Contributing**: Pull requests must include scope validation tests, audit log coverage, and dry-run compatibility.

---

## ⚖️ Legal & Ethical Disclaimer
`whisp3r` is engineered **exclusively for authorized red team engagements, penetration testing, and defensive research**. 
- Operators **must** possess written Rules of Engagement (RoE) before executing any offensive module.
- The framework enforces `--authorized` and scope validation to prevent unauthorized use.
- Developers assume **zero liability** for misuse, collateral damage, or legal violations.
- Deploy only in isolated labs or environments with explicit, documented consent.

---
🔐 *whisp3r Framework v3.2.0 | Dual-Tier. Scope-Enforced. Intelligence-First.*
