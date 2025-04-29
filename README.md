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

![Resource Group Creation](images/Resource%20Group%20Creation.png)

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
