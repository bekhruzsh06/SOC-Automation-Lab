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

#### 2.1 Windows 10

The Windows 10 client serves as the endpoint machine that generates security events and telemetry data.

To set it up, I downloaded the Windows 10 ISO image and created a new virtual machine in VirtualBox.

After installation, I downloaded Sysmon on the client from the microsoft website https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon and installed the config from the github https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml

```powershell
sysmon64.exe -i sysmonconfig.xml
```

<img width="573" height="178" alt="изображение" src="https://github.com/user-attachments/assets/e52974bc-96f8-48d4-b142-a40ff3346a0b" />



#### 2.2 Host 1 installation for Wazuh

This host was deployed on cloud with 4 CPUs and 8GB RAM for keeping Wazuh on it

Wazuh was installed using the instructions from the oficial website https://documentation.wazuh.com/current/quickstart.html 

```powershell
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh && sudo bash ./wazuh-install.sh -a
```

After I got the credentials, I was able to log in to the dashboard by searching public IP of the host and entering username and password

<img width="1900" height="836" alt="изображение" src="https://github.com/user-attachments/assets/0d5f8758-9075-4ecc-b6cb-d3d3f3b6640d" />

#### 2.3 Host 2: TheHive

This host was deployed on cloud with 6 CPUs and 16GB RAM for keeping TheHive on it

TheHive was installed using the instructions on the official website https://docs.strangebee.com/thehive/installation/installation-guide-linux-standalone-server/

Steps:

1. Install dependencies

2. Set up the Java virtual machine (JVM)

3. Install and configure Apache cassandra

4. Install and configure Elasticsearch

5. Install and configure TheHive



## Stage 3: Configuration

On this stage, I configured Wazuh and TheHive 

### 3.1 Configuring TheHive

Firstly, I configured cassandra 

```powershell
nano /etc/cassandra/cassandra.yaml
```
1. Changed the cluster name to 'bek'

<img width="880" height="101" alt="изображение" src="https://github.com/user-attachments/assets/30a82167-ca3a-4ae1-b5d6-776fa0c5e608" />

2. Set listen IP address and rpc address to the public IP of TheHive host

<img width="652" height="92" alt="изображение" src="https://github.com/user-attachments/assets/9d900cf8-0694-4936-a43a-2b9f117cb50f" />

<img width="448" height="65" alt="изображение" src="https://github.com/user-attachments/assets/f0cb0faa-4326-4d5c-bede-79a3bef196a1" />


3. Set seeds to the public IP of TheHive host

 <img width="833" height="152" alt="изображение" src="https://github.com/user-attachments/assets/5f002288-2536-4536-bd5e-880bb9c64211" />

After that, restarted cassandra service

<img width="1888" height="431" alt="изображение" src="https://github.com/user-attachments/assets/12077883-6b2d-47a0-85fe-ba2a00c1da3c" />

Then, configured elasticsearch

```powershell
nano /etc/elasticsearch/elasticsearch.yml
```

1. Uncommented and modified cluster.name and node.name

<img width="1048" height="228" alt="изображение" src="https://github.com/user-attachments/assets/b2bc019f-0ea8-4c54-af81-f1f38535e3fe" />


2. Changed network.host to the public IP of TheHive host

3. Start elasticsearch service

During starting process I also encountered an error:

<img width="1858" height="543" alt="изображение" src="https://github.com/user-attachments/assets/920a8099-c23d-45bf-8339-b900885193f0" />

And after troubleshooting, I found out that I have a duplicate lines, commenting one of them fixed the error

<img width="1891" height="92" alt="изображение" src="https://github.com/user-attachments/assets/a3bed3e1-90d2-46bf-bbdd-79a3139a5263" />

<img width="1097" height="83" alt="изображение" src="https://github.com/user-attachments/assets/c359b1b4-22d8-4f47-bbec-0e6d8296d8cc" />

<img width="737" height="141" alt="изображение" src="https://github.com/user-attachments/assets/d865d4b9-ed2e-40cf-95ee-8d6f15d52de5" />

After fixing the error, I restarted elasticsearch and it worked properly

<img width="1892" height="459" alt="изображение" src="https://github.com/user-attachments/assets/9b85b2aa-b193-4161-80aa-9d51db67e724" />

After starting elasticsearch I moved to thehive configuraition
 
1. Changed ownership of thehive directory to thehive user

```powershell
root@TheHive:~# cd /opt/thp
root@TheHive:/opt/thp# chown -R thehive:thehive /opt/thp
```

<img width="763" height="160" alt="изображение" src="https://github.com/user-attachments/assets/0cd7bf73-51d5-4299-8cdd-b9dd9c25f213" />

2. Configured thehive application

```powershell
nano /etc/thehive/application.conf
```

Changed hostname to IP of theghive host and named cluster accordingly

<img width="864" height="504" alt="изображение" src="https://github.com/user-attachments/assets/55fa6493-6e00-4ab5-8734-e8c03265bd2f" />

And also changed application url IP address from localhost to the address of the host

<img width="689" height="85" alt="изображение" src="https://github.com/user-attachments/assets/cd895f8e-dcfc-4c82-b5d3-ffb29a1fcd94" />

Then, started and enabled thehive

<img width="1891" height="412" alt="изображение" src="https://github.com/user-attachments/assets/f7e0d8bd-bd23-4451-991a-54599e58936e" />

Following that, I entered the url address and entered the dashboard

<img width="1915" height="847" alt="изображение" src="https://github.com/user-attachments/assets/92a203ef-f195-43e9-ba41-48263cbc42ac" />

