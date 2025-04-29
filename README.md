```markdown
# Azure SOC Home Lab - Honeypot + Sentinel SIEM

This project documents the creation of my first cybersecurity home lab using Microsoft Azure.  
The goal is to simulate real-world attack scenarios using a Windows honeypot and analyze the logs with Microsoft Sentinel.

---

## 🧠 Objective

To build a vulnerable lab environment on Azure that allows for:
- Simulated attacker behavior
- Log collection and analysis
- SIEM monitoring with Microsoft Sentinel

---

## 🏗️ Lab Architecture

### 1. Azure Account
- Free-tier Azure account created using:  
  [https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account](https://azure.microsoft.com/en-us/pricing/purchase-options/azure-account)

### 2. Resource Group
- **Name**: `EW-SOC-Lab`
- **Region**: `East US`

### 3. Virtual Network
- **Name**: `Vnet-soc-lab`
- **Address Space**: `10.0.0.0/16`
- **Subnet**: `10.0.0.0/24`

### 4. Honeypot Virtual Machine
- **Name**: `CORP-NET-EAST-1`
- **OS**: `Windows 10 Pro`
- **Size**: `Standard_B1s`
- **Public IP**: Enabled
- **Firewall**: Disabled via `wf.msc`
- **NSG**: All inbound traffic allowed (`Any → Any`)
- **Username**: `labuser`
- **Password**: `@labuser@365`

---

## 🧪 Attack Simulation & Log Collection

### Actions Performed:
- Ping tested the public IP (`52.x.x.x`)
- Made 4 failed login attempts to generate Event ID `4625`
- Used **Event Viewer** to verify logs under `Windows Logs > Security`

### Log Analytics Workspace
- **Name**: `LAW-soc-lab-0001`
- Logs sent to Microsoft Sentinel for analysis

---

## 📈 SIEM Configuration (Microsoft Sentinel)

- Enabled Microsoft Sentinel on the LAW workspace
- Installed `Windows Security Events` content from Content Hub
- Created **Data Collection Rule** via Azure Monitor Agent (AMA)
- Ran queries using **KQL Mode**

#### Example KQL Query:
```kusto
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Account, Computer, IpAddress
```

---

## 🌍 Geolocation & Visualization

- Uploaded a `geoip.csv` to Microsoft Sentinel Watchlist
- Queried using:
```kusto
_GetWatchlist("geoip")
```
- Created a visual **attack map** using a custom Workbook JSON (`map.json`)

---

## 📁 Project Structure

```plaintext
home-lab/
│
├── README.md
├── /images    <- Screenshots of setup and logs    
└── LICENSE    <- MIT License              
```

---

## 📸 Screenshots

All screenshots are stored in the `/images` directory, including:
- Resource Group creation
- NSG configuration
- Event Viewer logs
- KQL queries
- Geolocation results
- Sentinel attack map

---

## ✅ Project Status

- [x] Azure infrastructure created
- [x] Honeypot deployed
- [x] Logs collected successfully
- [x] Sentinel SIEM integrated
- [x] GeoIP Watchlist and Attack Map functional

---

## 🚧 Future Improvements

- Add Active Directory simulation (Windows Server)
- Enable alerting rules in Sentinel
- Automate log parsing and alert response

---

## 📜 License

This project is licensed under the MIT License.  
Feel free to use it for personal or educational purposes.

