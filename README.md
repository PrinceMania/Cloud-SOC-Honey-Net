# Building a SOC + Honeynet in Azure (Live Traffic)
![Full Project Map](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/f75de645-2077-4ce3-82d6-1c421a399d48)


## Introduction

In this project, I created a mini honeynet within the Azure cloud platform. The primary objective was to gather log data from diverse resources and channel them into a centralized Log Analytics workspace. This workspace is utilized by Microsoft Sentinel to effectively construct attack maps, set off alerts, and create incidents in response to potential threats.

To assess the effectiveness of the implemented security measures, I initially measured various security metrics while the environment remained vulnerable for a 24-hour period. Following that, I implemented specific security controls to enhance the environment's defences.  Subsequently, I conducted another 24-hour measurement of security metrics, comparing the results to those obtained in the insecure setting.
 The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Microsoft Sentinel / Workbook
![(After)Microsoft-Workbook](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/ac488317-1f44-4d20-9c96-c1bb242fc175)

## Architecture Before Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/aBDwnKb.jpg)

## Architecture After Hardening / Security Controls
![Architecture Diagram](https://i.imgur.com/YQNa9Pp.jpg)

The architecture of the mini honeynet in Azure consists of the following components:

- Virtual Network (VNet)
- Network Security Group (NSG)
- Virtual Machines (2 windows, 1 linux)
- Log Analytics Workspace
- Azure Key Vault
- Azure Storage Account
- Microsoft Sentinel

In the "BEFORE" phase, all resources were initially deployed and exposed to the internet. The Virtual Machines had both their Network Security Groups and built-in firewalls set to allow all traffic, leaving them wide open to potential threats. Additionally, all other resources were deployed with public endpoints accessible from the Internet, without utilizing Private Endpoints.

In the "AFTER" phase, significant security improvements were made. The Network Security Groups were hardened by blocking ALL traffic, except for connections originating from my admin workstation. Furthermore, all other resources were strengthened with their built-in firewalls and additionally protected using Private Endpoints, limiting their exposure to the Internet.

These security enhancements aimed to create a more robust and secure environment, and the "BEFORE" and "AFTER" metrics will reflect the impact of these measures on the overall security posture.


## Attack Maps Before Hardening / Security Controls
![(Before)-nsg-malicious-allowed-in](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/44ac18d1-1bc0-4c14-b119-edf75a6ca6e8)
<br>

![(Before)-syslog-ssh-auth-fail](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/6796531a-63b2-4156-98e2-1168523f2335)

![(Before)-windows-rdp-smb-auth-fail](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/d412e69a-e6d4-4774-8157-f9b50c116fe5)


## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-07-28 09:46:39
Stop Time 2023-07-29 09:46:39

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 75961
| Syslog                   | 4913
| SecurityAlert            | 14
| SecurityIncident         | 122
| AzureNetworkAnalytics_CL | 2949

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-07-30 03:22:15
Stop Time	2023-07-30 03:22:15

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent            | 6265
| Syslog                   | 0
| SecurityAlert            | 0
| SecurityIncident         | 0
| AzureNetworkAnalytics_CL | 0

## Conclusion

In this project, I created a mini honeynet using Microsoft Azure and integrated log sources into a Log Analytics workspace. Microsoft Sentinel played a key role in triggering alerts and managing incidents based on the logs collected. To assess the security of the environment, I measured specific metrics while operating in an insecure setup and then repeated the measurements after implementing security controls.

The implementation of security controls had a significant impact on the number of security events and incidents. The metrics clearly showed a considerable reduction in these events, indicating the effectiveness of the security measures.

However, it is important to note that if the network resources were heavily used by regular users, the number of security events and alerts could have been higher within the 24-hour period following the security control implementation. Higher resource utilization might have led to more security events being triggered, but the focus here is on the positive impact of the security measures in mitigating potential threats.

