# SOC-Automation-Lab

## Overview

A hands-on SOC homelab project designed to simulate a modern Security Operations Center (SOC) environment and develop practical blue team skills in threat detection, incident response, security monitoring, and SOC automation.

The lab integrates SIEM, XDR, SOAR, endpoint telemetry, threat intelligence enrichment, and automated incident handling workflows to emulate real-world SOC operations and analyst processes.


## Tools Used

- Wazuh - SIEM & XDR
- TheHive - Case Management
- Shuffle - SOAR


## Skills Learned


## Steps

### Step 1: Design

The purpose of this step was to design a logical diagram to undersrand the workflow of the system. The diagram was drawn on draw.io website
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


