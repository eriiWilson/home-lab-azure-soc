# Azure SOC Home Lab

This repository documents a small security monitoring lab I built while learning Microsoft Azure and Microsoft Sentinel.

The lab used a Windows virtual machine exposed to the internet to generate authentication events. These events were collected in a Log Analytics workspace and analyzed with KQL in Microsoft Sentinel.

This was a temporary learning environment, not a production-ready architecture.

## What I wanted to learn

My main goal was to understand the basic flow between an Azure resource and a SIEM:

1. Generate security events on a Windows virtual machine.
2. Send the events to Azure.
3. Find relevant records using KQL.
4. Enrich source IP addresses with geographical data.
5. Display the results in a Microsoft Sentinel workbook.

## Architecture

The environment contained the following resources:

- Azure resource group
- Virtual network and subnet
- Windows 10 virtual machine
- Network Security Group
- Log Analytics workspace
- Azure Monitor Agent
- Data Collection Rule
- Microsoft Sentinel
- Sentinel watchlist
- Custom workbook

```text
Internet
   |
   v
Public IP
   |
Network Security Group
   |
Windows 10 VM
   |
Azure Monitor Agent
   |
Data Collection Rule
   |
Log Analytics Workspace
   |
Microsoft Sentinel
   |
KQL queries and workbook
```

## Azure resources

| Resource | Configuration |
|---|---|
| Resource group | `EW-SOC-Lab` |
| Region | East US |
| Virtual network | `Vnet-soc-lab` |
| Address space | `10.0.0.0/16` |
| Subnet | `10.0.0.0/24` |
| Virtual machine | `CORP-NET-EAST-1` |
| Operating system | Windows 10 Pro |
| VM size | `Standard_B1s` |

![Resource group creation](images/resource%20group%20creation.png)

## Virtual machine exposure

For this lab, the virtual machine received a public IP address and its inbound network rules were intentionally made permissive.

Windows Firewall was also disabled during the test.

This configuration made the machine deliberately vulnerable and should not be reproduced in a production environment. A safer version of the lab should limit the exposure time, avoid storing real data and remove all resources after testing.

Credentials and complete public IP addresses are not included in this repository.

## Generating authentication events

I first generated failed sign-in events manually by attempting to authenticate with invalid credentials.

I then opened Windows Event Viewer and checked:

```text
Windows Logs > Security
```

The failed authentication attempts appeared as Windows Security Event ID `4625`.

This confirmed that Windows was recording the events locally before I configured their collection in Azure.

## Connecting the VM to Microsoft Sentinel

I created a Log Analytics workspace and enabled Microsoft Sentinel on it.

To collect Windows security events, I:

1. Installed the Windows Security Events solution from Content Hub.
2. Installed the Azure Monitor Agent on the virtual machine.
3. Created a Data Collection Rule.
4. Associated the rule with the Windows VM.
5. Confirmed that security events were reaching the workspace.

I used the following KQL query to find failed authentication events:

```kusto
SecurityEvent
| where EventID == 4625
| project TimeGenerated, Computer, Account, IpAddress
| order by TimeGenerated desc
```

This query filters the `SecurityEvent` table for failed logon events and returns the time, computer, account and source IP address associated with each record.

## IP geolocation

To add geographical context to the source IP addresses, I uploaded a GeoIP dataset as a Microsoft Sentinel watchlist.

The watchlist could be inspected with:

```kusto
_GetWatchlist("geoip")
```

I then used the IP information from the security events together with the watchlist data to identify approximate locations.

Geolocation based on an IP address is not exact. The results indicate the approximate location associated with an address and should not be treated as proof of an attacker's physical location.

## Workbook visualization

I imported a custom workbook definition from `map.json` to display authentication activity on a map.

![Windows VM attack map](images/windows%20vm%20attack%20map.png)

The workbook helped me understand how raw Windows events can be transformed into a more readable security monitoring view.

## What I learned

This project helped me understand:

- How Windows records failed authentication attempts.
- How the Azure Monitor Agent collects events from a VM.
- The role of a Data Collection Rule.
- How Log Analytics and Microsoft Sentinel work together.
- How to filter security events using KQL.
- How watchlists can enrich event data.
- Why publicly exposed services generate security events quickly.
- Why permissive NSG rules and disabled host firewalls are unsafe outside an isolated lab.

## Limitations

This was an introductory lab with a deliberately simple architecture.

It did not include:

- Automated incident response.
- Custom analytics rules.
- Microsoft Entra ID integration.
- A Windows domain environment.
- Long-term monitoring.
- Infrastructure as code.
- Production security controls.

The failed sign-in events generated manually were useful for validating the data pipeline, but they should not be described as a complete real-world attack simulation.

## Possible improvements

Future versions of the lab could include:

- More restrictive NSG rules and controlled exposure.
- Microsoft Sentinel analytics rules.
- Alerts for repeated authentication failures.
- Incident creation and investigation.
- Microsoft Entra ID authentication logs.
- Infrastructure deployment with Bicep or Terraform.
- Automated resource cleanup to control Azure costs.
- Documentation of the resource removal process.

## Repository structure

```text
.
├── README.md
├── images/
├── map.json
└── LICENSE
```

The `images` directory contains screenshots of the Azure resources, Windows events, KQL results and Sentinel workbook.

## Disclaimer

This project was created for educational purposes in an isolated Azure environment.

Do not expose a virtual machine with unrestricted inbound access, disabled firewall protection, reused credentials or sensitive information.
