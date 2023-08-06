# Building a SOC + Honeynet in Azure (Live Traffic)
![Full Project Map v2](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/e7606f92-1138-4726-a50a-1e1c2dcb4888)



## Introduction

In this project, I created a mini honeynet within the Azure cloud platform. The primary objective was to gather log data from diverse resources and channel them into a centralized Log Analytics workspace. This workspace is utilized by Microsoft Sentinel to effectively construct attack maps, set off alerts, and create incidents in response to potential threats.

To assess the effectiveness of the implemented security measures, I initially measured various security metrics while the environment remained vulnerable for a 24-hour period. Following that, I implemented specific security controls to enhance the environment's defences.  Subsequently, I conducted another 24-hour measurement of security metrics, comparing the results to those obtained in the insecure setting.
 The metrics we will show are:

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)

## Technologies, Regulations, and Azure Components Employed:

- Azure Virtual Network (VNet)
- Azure Network Security Group (NSG)
- Virtual Machines (2x Windows, 1x Linux)
- Log Analytics Workspace with Kusto Query Language (KQL) Queries
- Azure Key Vault for Secure Secrets Management
- Azure Storage Account for Data Storage
- Microsoft Sentinel for Security Information and Event Management (SIEM)
- Microsoft Defender for Cloud to Protect Cloud Resources
- Windows Remote Desktop for Remote Access
- Command Line Interface (CLI) for System Management
- PowerShell for Automation and Configuration Management
- [NIST SP 800-53 Revision 5](https://csrc.nist.gov/publications/detail/sp/800-53/rev-5/final) for Security Controls
- [NIST SP 800-61 Revision 2](https://www.nist.gov/privacy-framework/nist-sp-800-61) for Incident Handling Guidance


## Architecture Prior to Implementing Hardening Measures and Security Controls
![Un-Secured Network](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/25c763db-2d66-4c1b-b0ae-4a7f25e78d96)



<b>Before Hardening Measures and Security Controls:</b>

- In the "BEFORE" stage of the project, all resources were initially deployed with public exposure to the internet. This setup was intentionally insecure to attract potential cyber attackers and observe their tactics. The Virtual Machines had both their Network Security Groups (NSGs) and built-in firewalls wide open, allowing unrestricted access from any source. Additionally, all other resources, such as storage accounts and databases, were deployed with public endpoints visible to the internet, without utilizing any Private Endpoints for added security.

## Architecture After Implementing Hardening Measures and Security Controls
![Secured Network](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/122baadd-c443-4fb9-b589-2047320390f1)



 <b>For the "AFTER" stage, I implemented a series of hardening measures and security controls to improve the environment's overall security posture. These improvements included:</b>

- <b>Network Security Groups (NSGs)</b>: I hardened the NSGs by blocking all inbound and outbound traffic, with the sole exception of my own public IP address. This ensured that only authorized traffic from a trusted source was allowed to access the virtual machines.

- <b>Built-in Firewalls</b>: I configured the built-in firewalls on the virtual machines to restrict access and protect the resources from unauthorized connections. This step involved fine-tuning the firewall rules based on the specific requirements of each VM, thereby minimizing the potential attack surface.

- <b>Private Endpoints</b>: To enhance the security of other Azure resources, I replaced the public endpoints with Private Endpoints. This ensured that access to sensitive resources, such as storage accounts and databases, was limited to the virtual network and not exposed to the public internet. As a result, these resources were protected from unauthorized access and potential attacks.

By comparing the security metrics BEFORE and AFTER implementing these hardening measures and security controls, I was able to demonstrate the effectiveness of each step in improving the overall security posture of the Azure cloud environment.

## Attack Maps Before Hardening / Security Controls


- <b>This attack map demonstrates the consequences of leaving the Network Security Group (NSG) open, as it allowed for malicious traffic to flow unimpeded. This visualization underscores the importance of implementing proper security measures, such as restricting NSG rules, to prevent unauthorized access and minimize potential threats.</b>


![(Before)-nsg-malicious-allowed-in](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/44ac18d1-1bc0-4c14-b119-edf75a6ca6e8)
<br>

 - <b>This attack map highlights the numerous syslog authentication failures experienced by the Linux server I deployed, indicating that unauthorized access attempts were made from outisde. This serves as a reminder of the importance of securing Linux servers with strong authentication mechanisms and monitoring system logs for signs of intrusion attempts.</b>


![(Before)-syslog-ssh-auth-fail](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/6796531a-63b2-4156-98e2-1168523f2335)


 - <b>This attack map showcases numerous RDP and SMB failures, illustrating the persistent attempts by potential attackers to exploit these protocols. The visualization emphasizes the need for securing remote access and file sharing services to protect against unauthorized access and potential cyber threats.</b>


![(Before)-windows-rdp-smb-auth-fail](https://github.com/PrinceMania/Cloud-SOC-Honey-Net/assets/141386970/d412e69a-e6d4-4774-8157-f9b50c116fe5)


## Metrics Before Hardening / Security Controls

The following table shows the metrics we measured in our insecure environment for 24 hours:
Start Time 2023-07-28 09:46:39
Stop Time 2023-07-29 09:46:39

| Metric                   | Count
| ------------------------ | -----
|SecurityEvent (Windows VM)           | 75961
| Syslog (Linux VM)                 | 4913
| SecurityAlert (Microsoft Defender for Cloud)           | 14
| SecurityIncident (Sentinel Incidents)          | 122
| NSG Inbound Malicious Flows Allowed | 2949

## Attack Maps Before Hardening / Security Controls

```All map queries actually returned no results due to no instances of malicious activity for the 24 hour period after hardening.```

## Metrics After Hardening / Security Controls

The following table shows the metrics we measured in our environment for another 24 hours, but after we have applied security controls:
Start Time 2023-07-30 03:22:15
Stop Time	2023-07-30 03:22:15

| Metric                   | Count
| ------------------------ | -----
| SecurityEvent (Windows VM)             | 6265
| Syslog (Linux VM)                    | 11
| SecurityAlert (Microsoft Defender for Cloud)            | 0
| SecurityIncident (Sentinel Incidents)         | 0
| NSG Inbound Malicious Flows Allowed | 0

<br />

## Utilizing NIST 800.61r2 Computer Incident Handling Guide

For each simulated attack I then practiced incident responses following NIST SP 800-61 r2.

![NIST 800.61](https://i.imgur.com/6PTG7c0l.png)

Each organization will have policies related to an incident response that should be followed. This event is just a walkthrough for possible actions to take in the detection of malware on a workstation.  

#### Preparation

- The Azure lab was set up to ingest all of the logs into Log Analytics Workspace, Sentinel and Defender were configured, and alert rules were put in place.

#### Detection & Analysis

- Malware has been detected on a workstation with the potential to compromise the confidentiality, integrity, or availability of the system and data.
- Assigned alert to an owner, set the severity to "High", and the status to "Active"
- Identified the primary user account of the system and all systems affected.
- A full scan of the system was conducted using up-to-date antivirus software to identify the malware.
- Verified the authenticity of the alert as a "True Positive".
- Sent notifications to appropriate personnel as required by the organization's communication policies.

#### Containment, Eradication & Recovery

- The infected system and any additional systems infected by the malware were quarantined.
- If the malware was unable to be removed or the system sustained damage, the system would have been shut down and disconnected from the network.
- Depending on organizational policies the affected systems could be restored known clean state, such as a system image or a clean installation of the operating system and applications. Or an up-to-date anti-virus solution could be used to clean the systems. 

#### Post-Incident Activity

- In this simulated case, an employee had downloaded a game that contained malware. 
- All information was gathered and analyzed to determine the root cause, extent of damage, and effectiveness of the response. 
- Report disseminated to all stakeholders.
- Corrective actions are implemented to remediate the root cause.
- And a lessons-learned review of the incident was conducted.

<br />

## Conclusion

In this project, I created a mini honeynet using Microsoft Azure and integrated log sources into a Log Analytics workspace. Microsoft Sentinel played a key role in triggering alerts and managing incidents based on the logs collected. To assess the security of the environment, I measured specific metrics while operating in an insecure setup and then repeated the measurements after implementing security controls.

The implementation of security controls had a significant impact on the number of security events and incidents. The metrics clearly showed a considerable reduction in these events, indicating the effectiveness of the security measures.

However, it is important to note that if the network resources were heavily used by regular users, the number of security events and alerts could have been higher within the 24-hour period following the security control implementation. Higher resource utilization might have led to more security events being triggered, but the focus here is on the positive impact of the security measures in mitigating potential threats.

