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

#### Steps Overview

- Step 1: Wazuh Agent on Windows Client sends events to Wazuh Manager via Internet
- Step 2: Wazuh Manager receives events
- Step 3: Wazuh Manager sends alerts to Shuffle SOAR which decides what to do with them
- Step 4,5,6,7: Shuffle enrichs IOCs on VirusTotal, sends alerts to TheHive and sends email containing alert information to SOC analyst
- Step 8: SOC Analyst sends response actions to Shuffle which retranslates them to Wazuh Manager
- Step 9: Wazuh Manager performs response instructions given by SOC Analyst on Client machine


