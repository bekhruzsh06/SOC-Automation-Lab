# SOC-Automation-Lab

## Overview

A hands-on SOC homelab project designed to simulate a modern Security Operations Center (SOC) environment and develop practical blue team skills in threat detection, incident response, security monitoring, and SOC automation.

The lab integrates SIEM, XDR, SOAR, endpoint telemetry, threat intelligence enrichment, and automated incident handling workflows to emulate real-world SOC operations and analyst processes.


## Tools Used

- Wazuh - SIEM & XDR
- TheHive - Case Management
- Shuffle - SOAR


## Skills Learned


## Stages

### Stage 1: Design

The purpose of this stage was to design a logical diagram to undersrand the workflow of the system. The diagram was drawn on draw.io website
<p align="center">
  <img width="618" alt="SOC Workflow Diagram" src="https://github.com/user-attachments/assets/c2bcd054-3be0-4bbc-ac75-4f9d0f7e081e" />
</p>

<p align="center">
  <em>Figure 1: SOC Automation Workflow Diagram</em>
</p>

#### Workflow Overview

##### Step 1 — Endpoint Telemetry Collection
The Wazuh Agent installed on the Windows endpoint collects security events, Sysmon logs, and telemetry data, then securely forwards them to the Wazuh Manager over the Internet.

##### Step 2 — Event Processing & Correlation
The Wazuh Manager receives, parses, and correlates incoming events to identify suspicious or malicious activity based on detection rules and configured alert thresholds.

##### Step 3 — Alert Forwarding to SOAR Platform
Generated alerts are forwarded from Wazuh to Shuffle SOAR for automated incident handling and orchestration.

##### Step 4 — IOC Enrichment
Shuffle extracts Indicators of Compromise (IOCs) such as IP addresses, domains, hashes, or URLs from alerts and enriches them using VirusTotal threat intelligence services.

##### Step 5 — Incident Creation
Enriched alerts are automatically converted into incident cases within TheHive platform for centralized investigation and case management.

##### Step 6 — Analyst Notification
Shuffle sends automated email notifications containing alert details, threat intelligence results, and incident information to the SOC analyst.

##### Step 7 — Incident Investigation
The SOC analyst reviews the alert, validates malicious activity, analyzes enriched IOC data, and determines the appropriate response action.

##### Step 8 — Response Orchestration
Response actions selected by the SOC analyst are submitted to Shuffle, which forwards the required response instructions to the Wazuh Manager.

##### Step 9 — Automated Response Execution
The Wazuh Manager executes active response actions given by SOC analyst on the endpoint machine.



### Stage 2: Installation

On this stage, I installed all hosts, required for the project:

1. Windows 10 Client on VirtualBox
2. Wazuh server on cloud host
3. TheHive server on cloud host

#### 1. Windows 10

The Windows 10 client serves as the endpoint machine that generates security events and telemetry data.

To set it up, I downloaded the Windows 10 ISO image and created a new virtual machine in VirtualBox.

After installation, I downloaded Sysmon on the client from the microsoft website https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon and installed the config from the github https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml

```powershell
sysmon64.exe -i sysmonconfig.xml
```

<img width="573" height="178" alt="изображение" src="https://github.com/user-attachments/assets/e52974bc-96f8-48d4-b142-a40ff3346a0b" />



#### 2. Host 1 installation for Wazuh

This host was deployed on cloud with 4 CPUs and 8GB RAM for keeping Wazuh on it

Wazuh was installed using the instructions from the oficial website https://documentation.wazuh.com/current/quickstart.html 

```powershell
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

After I got the credentials, I was able to log in to the dashboard by searching public IP of the host and entering username and password

<img width="1900" height="836" alt="изображение" src="https://github.com/user-attachments/assets/0d5f8758-9075-4ecc-b6cb-d3d3f3b6640d" />

#### 3. Host 2: TheHive

This host was deployed on cloud with 6 CPUs and 16GB RAM for keeping TheHive on it

TheHive was installed using the instructions on the official website https://docs.strangebee.com/thehive/installation/installation-guide-linux-standalone-server/

Steps:

1. Install dependencies

2. Set up the Java virtual machine (JVM)

3. Install and configure Apache cassandra

4. Install and configure Elasticsearch

5. Install and configure TheHive





