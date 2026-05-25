<img width="442" height="523" alt="изображение" src="https://github.com/user-attachments/assets/b3e0f6a9-687f-49fd-a412-89eb2a9476fb" /># SOC-Automation-Lab

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


### 3.2 Configuring Wazuh

On my virtualbox Windows workstation I entered to dashboard and there clicked Deploy Agent to install the agent on the client

<img width="1880" height="855" alt="изображение" src="https://github.com/user-attachments/assets/e0be52e5-e6ac-44ce-93a8-119a80f864e2" />

Configured accordingly and ran the powershell command given by Wazuh

<img width="977" height="631" alt="изображение" src="https://github.com/user-attachments/assets/4f01e567-f3f3-47af-950a-df709c388f82" />

```powershell
Invoke-WebRequest -Uri https://packages.wazuh.com/4.x/windows/wazuh-agent-4.14.5-1.msi -OutFile $env:tmp\wazuh-agent; msiexec.exe /i $env:tmp\wazuh-agent /q WAZUH_MANAGER='192.248.147.195' WAZUH_AGENT_NAME='bek-windows10'
```

And start wazuh

```powershell
net start wazuh
```

<img width="499" height="125" alt="изображение" src="https://github.com/user-attachments/assets/7de29bf8-0c9a-4b7d-8b03-5d5520323705" />

And then from the dashboard we can see that the agent was added successfully

<img width="1332" height="626" alt="изображение" src="https://github.com/user-attachments/assets/d286f458-e079-419f-8808-894779ea9593" />


### Stage 4: Configuring Windows 10 host

On this stage, we are configuring windows 10 host, sending sysmon telemtry to wazuh manager and creating custom detection rules for mimikatz

### 4.1 Configuring host for Sysmon telemetry ingestion

Firstly, we need to configure ossec.conf file, which is the primary configuration file for wazuh agent

<img width="795" height="452" alt="изображение" src="https://github.com/user-attachments/assets/a45ed6a3-5e75-4a5d-8707-c061378bce0c" />

Here, log analysis shows which event logs are ingested into wazuh

So we should replace it with sysmon logs instead

<img width="643" height="560" alt="изображение" src="https://github.com/user-attachments/assets/80c5e67c-0f09-48c5-b948-47d7c44774c9" />

Copied the full name from event viewer and inserted into conf file

<img width="592" height="110" alt="изображение" src="https://github.com/user-attachments/assets/881c8ffa-fce6-403b-8dd1-2222bc204795" />

Now if we search for Sysmon in ElasticSearch, we notice that it is present

<img width="1018" height="485" alt="изображение" src="https://github.com/user-attachments/assets/d437bd08-7fd5-4c74-92b5-20d4ff73b7c6" />

### 4.2 Generating Mimikatz telemetry

On this step, we will generate mimikatz telemetry to see patterns that we can use further on rule development

First, we need to disable Windows Defender Firewall for our attack to go smoothly

<img width="777" height="851" alt="изображение" src="https://github.com/user-attachments/assets/6e902789-21a0-4fa5-9ee4-49e8d0891f07" />

After that, I downloaded mimikatz from github, and executed it on my Windows 10 client

<img width="762" height="177" alt="изображение" src="https://github.com/user-attachments/assets/ab507504-a85a-496a-8ef4-7ef7585590f3" />

But to capture mimikatz logs, we need enable archiving of logs, which allows us to store and query raw data, which may not be captured by standard alert rules.

To do so, we first configure the ossec.conf file on manager

```bash
nano /var/ossec/etc/ossec.conf
```

Changle logall and alertall to "yes"

<img width="502" height="79" alt="изображение" src="https://github.com/user-attachments/assets/34b49a9a-b344-47bf-b668-2db8c4b5f6f6" />


And archives enabled to yes

<img width="557" height="158" alt="изображение" src="https://github.com/user-attachments/assets/f1a5a10e-b741-4f2e-af7f-3b2e2c0b065d" />

Then we should create an indexer on elasticsearch to make the data, containing mimikatz searchable

On the dashboard management, we click go to index patterns

<img width="246" height="171" alt="изображение" src="https://github.com/user-attachments/assets/d591f96b-5490-4e03-bbcb-422cddefd319" />

Create index pattern

<img width="1209" height="483" alt="изображение" src="https://github.com/user-attachments/assets/751f293b-19e7-4aee-90bd-a178d9281a95" />

<img width="1182" height="323" alt="изображение" src="https://github.com/user-attachments/assets/3671e657-8e4f-47f1-a7a5-ceb17543bc60" />

Now on the index patterns, we can find our archives index

<img width="358" height="239" alt="изображение" src="https://github.com/user-attachments/assets/5b2e4161-204d-40ee-9e29-43d1d3983c2a" />

Now after choosing the index pattern we created and searching for mimikatz, we can find events, related it

<img width="1876" height="764" alt="изображение" src="https://github.com/user-attachments/assets/81b9bc91-c201-437d-a1b5-e8d7a5be9c06" />


#### 4.3 Creating a rule for mimikatz detection

On the rules section, clicked custom rules and edited the rule to detect mimikatz

<img width="1838" height="285" alt="изображение" src="https://github.com/user-attachments/assets/fff2c054-29d1-45e1-aad6-318105a5bc01" />

edit the local_rules.xml file and add the rule, that will detect sysmon event id 1 (Process Creation) and OriginalFileName field - mimikatz.exe

<img width="866" height="174" alt="изображение" src="https://github.com/user-attachments/assets/5bad9a78-c8a0-479d-b779-59738aabda2e" />

Now searching for the mimikatz from wazuh-alerts* index, triggers the rule

<img width="1870" height="630" alt="изображение" src="https://github.com/user-attachments/assets/fabcf0ea-b026-448b-8e6e-2e08e2c51c80" />

We can also see the description of the rule:

<img width="665" height="249" alt="изображение" src="https://github.com/user-attachments/assets/1bc6126f-55df-43a6-92c2-10ceb36e5a50" />

## Stage 5: Creating Automation

On this stage, we are going to automate the workflow of Wazuh and TheHive using Shuffle

#### 5.1 Linking Wazuh to shuffle

Firstly, we are creating webhook and adding it to the ossec configuration file to link wazuh and shuffle

<img width="1024" height="514" alt="изображение" src="https://github.com/user-attachments/assets/19d4ef0e-879c-4930-8f07-d3212258b5bb" />

We should copy webhook's URI and add it to the offsec.conf

<img width="401" height="185" alt="изображение" src="https://github.com/user-attachments/assets/6d829338-2882-4d68-a857-5bab9d2a7776" />

<img width="1325" height="171" alt="изображение" src="https://github.com/user-attachments/assets/416182b3-cc8c-486d-b18e-cdb912aaf7bc" />


And after running mimikatz.exe on the Windows client again, we can notice that webhook captures this event based on rule

<img width="566" height="804" alt="изображение" src="https://github.com/user-attachments/assets/58ee52cd-19c1-4103-90fe-2071bb512f47" />


#### 5.2 Extracting SHA256 hash and passing it to VirusTotal

Initially, we are making shuffle tool to capture data of the field that stores SHA256. Tool is going to capture it by using regex

<img width="996" height="522" alt="изображение" src="https://github.com/user-attachments/assets/66465168-6a25-46d7-843a-5628088401d7" />

```
SHA256=([0-9A-Fa-f]{64})
```

Then, we are linking VirusTotal to the regex parser, so it will parse the hash into VirusTotal

<img width="810" height="286" alt="изображение" src="https://github.com/user-attachments/assets/6931f750-63a2-4318-89dd-a995499e12dc" />


Grabbing our API key from the website and passing to the authenticator

<img width="1887" height="356" alt="изображение" src="https://github.com/user-attachments/assets/7ec55685-3e10-4d64-a4e5-01b5dbe6ef9a" />

Choosing the input to parse in VirusTotal

<img width="573" height="742" alt="изображение" src="https://github.com/user-attachments/assets/083bd676-ba85-424c-b6dd-d6b7e6408bc2" />

After running that workflow, we can see that http code is 200, so hash parsed to VirusTotal successfully

<img width="535" height="669" alt="изображение" src="https://github.com/user-attachments/assets/ef1cc0e8-09f2-445a-b21a-f26068eecf88" />


#### 5.3 Linking VirusTotal with TheHive

Now, we need to send the report from VirusTotal to TheHive

Firstly, we are linking TheHive and VirusTotal

<img width="878" height="476" alt="изображение" src="https://github.com/user-attachments/assets/a0317968-947c-4bf5-9d49-f9e61b315d0a" />

And creating an organisation in TheHive for alerts forwarding from shuffle

<img width="855" height="808" alt="изображение" src="https://github.com/user-attachments/assets/297101df-8e3d-4496-8ee3-99bbec0031b3" />

Then, creating a user inside of the organisation to log in into platform

<img width="850" height="487" alt="изображение" src="https://github.com/user-attachments/assets/e7d95663-91d0-45a8-b3a4-c9181016586d" />

And service account for creating an API key for authentication in shuffle

<img width="859" height="684" alt="изображение" src="https://github.com/user-attachments/assets/5276d78c-d2d2-44a6-9890-7d3ea11c1b76" />

Create and paste the API key to TheHive in shuffle

<img width="849" height="179" alt="изображение" src="https://github.com/user-attachments/assets/58e71771-cbd8-4191-8cb0-1cb87f1e7958" />

<img width="544" height="627" alt="изображение" src="https://github.com/user-attachments/assets/362e51ec-5104-4996-b77c-dff7f2188adc" />

And after that, we are sending an event to TheHive with the following Body

```
{
  "description": "$exec.title",
  "externallink": "${externallink}",
  "flag": false,
  "pap": 2,
  "severity": "3",
  "source": "$exec.pretext",
  "sourceRef": "$exec.rule_id",
  "status": "New",
  "summary": "Mimikatz activity detected on host $exec.text.win.system.computer",
  "tags": ["T10003"],
  "title": "$exec.title",
  "tlp": "2",
  "type": "internal"
}
```

<img width="442" height="523" alt="изображение" src="https://github.com/user-attachments/assets/50dc795f-51ff-42e2-95af-b1a98576d4ad" />


And after running that workflow, we can see that alert was sent successfully

<img width="549" height="441" alt="изображение" src="https://github.com/user-attachments/assets/9fdfbbf4-0852-4758-8080-58597632ab58" />

If we log in to our newly created normal account, we can see it as well

<img width="1909" height="341" alt="изображение" src="https://github.com/user-attachments/assets/1b16dc48-843b-4281-9e69-a622e030f557" />


<img width="1332" height="750" alt="изображение" src="https://github.com/user-attachments/assets/9daa3a46-13a9-4bed-a3ae-7cc4a11c264b" />


#### 5.4 Sending email to SOC analyst

First drag the email app into the workflow and connect it to the VirusTotal

<img width="1398" height="720" alt="изображение" src="https://github.com/user-attachments/assets/d7494c11-5260-4be9-8691-28bd4f015c54" />

And after running that workflow again, we can see the email sent to us with the appropriate message

<img width="1604" height="465" alt="изображение" src="https://github.com/user-attachments/assets/f5469581-2fdc-49a3-b304-67765f21a632" />
