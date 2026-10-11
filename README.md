<div align="center">

# 🪟 Windows CIS Hardening with Ansible

**Automating CIS Benchmark controls on Windows hosts, with verification, error handling and a compliance report**

![Ansible](https://img.shields.io/badge/Ansible-EE0000?logo=ansible&logoColor=white)
![Windows](https://img.shields.io/badge/Windows-0078D6?logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-5391FE?logo=powershell&logoColor=white)
![CIS](https://img.shields.io/badge/CIS-Benchmarks-1f6feb)

</div>

---

## 📌 Overview

An automation project that applies a set of **CIS Microsoft Windows Benchmark** controls over WinRM with one playbook. Each control is tagged with its CIS ID so it can be run on its own. Failures are written to the Windows Application event log, and the run ends with an HTML compliance report.

## 🛡️ Controls implemented

| CIS ID | Control | Task file |
|---|---|---|
| 1.2.2 / 1.2.3 | Account lockout threshold and reset counter | [`account_policies.yml`](roles/cis_windows/tasks/account_policies.yml) |
| 2.3.7.7 | Password expiry warning | [`account_policies.yml`](roles/cis_windows/tasks/account_policies.yml) |
| 2.3.1.2 | Disable the Guest account (with verification) | [`user_accounts.yml`](roles/cis_windows/tasks/user_accounts.yml) |
| 2.3.7.2 | Require CTRL+ALT+DEL; legal notice banner | [`logon_settings.yml`](roles/cis_windows/tasks/logon_settings.yml) |
| 2.2.7 | Restrict RDP to Administrators; block built-in Administrator remote access | [`remote_access.yml`](roles/cis_windows/tasks/remote_access.yml) |
| 9.x | Windows Firewall enabled on all profiles, with fallbacks | [`firewall_settings.yml`](roles/cis_windows/tasks/firewall_settings.yml) |

## 🗂️ Structure

```
site.yml                         # playbook: applies the cis_windows role to [windows]
inventory.example.ini            # WinRM inventory template
roles/cis_windows/
  defaults/main.yml              # CIS target values (override per environment)
  tasks/main.yml                 # orchestrates the sections; block/rescue error handling
  tasks/*.yml                    # one file per control family
  templates/                     # legal notice and HTML compliance report
```

## ▶️ Usage

```bash
pip install ansible pywinrm
cp inventory.example.ini inventory.ini        # add your hosts
ansible-playbook -i inventory.ini site.yml                    # apply everything
ansible-playbook -i inventory.ini site.yml --tags firewall    # one section only
ansible-playbook -i inventory.ini site.yml --check            # dry run
```

The report is written to `C:\CIS_Compliance_Report.html` on each host.

## ✨ Design choices

- **Idempotent:** tasks report `changed` only when a setting actually changes.
- **Resilient:** `block`/`rescue` sections log failures to the Windows event log instead of stopping the whole run.
- **Traceable:** every task carries its CIS control number as a tag.
- **No secrets in the repo:** credentials belong in Ansible Vault, and inventory and vault files are git-ignored.

---

<sub>Author: <b>Rabbi Muhammad Aman</b> · Built for an automation course project and tested only on lab VMs. <code>defaults/</code>, the templates and the inventory example were added so the role runs as published.</sub>
