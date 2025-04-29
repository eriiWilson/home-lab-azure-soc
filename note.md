
```markdown
# Home Lab Project Notes

This document details the steps taken during the creation of my first cybersecurity home lab using Microsoft Azure.

---

## 1. Azure Account Creation

- Created a free-tier Azure account using the official portal:
  [https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account)
- Verified eligibility for free services:
  - B1s Virtual Machines
  - Virtual Networks
  - Microsoft Sentinel (with workspace limits)
- Initial credit: $200 (USD) valid for 30 days.
- Ensured no premium services were accidentally enabled.

---

## 2. Resource Group and Virtual Network Setup

### Resource Group
- **Name**: `EW-SOC-Lab`
- **Region**: `East US`

### Virtual Network
- **Name**: `Vnet-soc-lab`
- **Address Space**: `10.0.0.0/16`
- **Subnet**: `default (10.0.0.0/24)`

### Security Settings
- Azure Bastion: Disabled
- Azure Firewall: Disabled
- Azure DDoS Protection: Disabled

---

## 3. Honeypot Virtual Machine Setup

### VM Configuration
- **Name**: `CORP-NET-EAST-1`
- **Operating System**: `Windows 10 Pro`
- **Size**: `Standard_B1s`
- **Availability Zone**: Zone 2 (Auto-selected)

### Network Configuration
- Attached to `Vnet-soc-lab`, `default` subnet.
- Public IP: Dynamic (assigned automatically).
- Network Security Group: Inbound rule set to allow `Any -> Any` traffic for simulation.

### System Configuration
- Username: `labuser`
- Password: `@labuser@365`
- Disabled Windows Defender Firewall across all profiles using `wf.msc`.

---

## 4. Attack Simulation

- Pinged the VM's public IP (`52.146.21.69`) successfully from an external machine.
- Attempted 4 failed logins with wrong credentials.
- Accessed **Event Viewer** under `Windows Logs > Security`.
- Searched for Event ID `4625` to confirm failed login attempts were recorded.

---

## 5. Log Analytics Workspace Setup

- Created a **Log Analytics Workspace**:
  - **Name**: `LAW-soc-lab-0001`
  - **Region**: `East US`

- Enabled **Microsoft Sentinel** on the workspace.
- Installed the **Windows Security Events** content pack from Content Hub.
- Configured a **Data Collection Rule** via Azure Monitor Agent (AMA) to collect Windows security logs.

---

## 6. Microsoft Sentinel and KQL Queries

- Accessed Sentinel Logs in **KQL Mode**.
- Sample Query for failed logins:

```kusto
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Account, Computer, IpAddress
| sort by TimeGenerated desc
```

- Queried `SecurityEvent` table successfully.

---

## 7. Geolocation (GeoIP Integration)

- Downloaded a `geoip.csv` containing IP address geolocation data.
- Created a **Watchlist** in Microsoft Sentinel named `geoip`.
- Queried the Watchlist using:

```kusto
_GetWatchlist("geoip")
```

- Mapped public IPs to countries, cities, and coordinates.

---

## 8. Attack Visualization (Workbook)

- Downloaded a custom **map.json** template for Azure Workbooks.
- Imported the template into Microsoft Sentinel to create a **global attack map** visualizing login attempts geographically.

---

## 9. Project Status

- [x] Azure resources successfully deployed
- [x] Honeypot VM exposed and vulnerable
- [x] Event logs collected and verified
- [x] SIEM (Microsoft Sentinel) operational
- [x] Geolocation Watchlist integrated
- [x] Global attack map visualization created

---

## 10. Future Improvements

- Deploy a second VM simulating an Active Directory Domain Controller.
- Configure automatic alert rules in Microsoft Sentinel for brute force attacks.
- Expand KQL queries to detect unusual login behavior.
- Implement Sysmon agent for deeper endpoint detection.
- Create Playbooks for automated incident response.

---
