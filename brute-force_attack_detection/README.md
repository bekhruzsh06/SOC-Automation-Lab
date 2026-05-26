
For this section, I'm going to simulate a brute-force attack on Windows host from Kali linux via SSH, detect it, develop a rule and create an automation to notify SOC analyst about an attack

## Stage 1: Configure VirtualBox network adapters

When I firstly executed two machines, I noticed that both kali and windows shared the same IP address, so I had to give them two distinct IP addresses, so they will be able to communicate properly with each other

Therefore, I created a NAT network on VirtualBox 

<img width="1116" height="265" alt="изображение" src="https://github.com/user-attachments/assets/25aef3e4-373d-4465-8d95-04fb04a51463" />


And set it as a NAT network for each VM

Kali:
<img width="818" height="282" alt="изображение" src="https://github.com/user-attachments/assets/547dac0e-390f-4a5c-8111-65c52f709d5a" />

Windows:
<img width="823" height="302" alt="изображение" src="https://github.com/user-attachments/assets/d7c4e83b-e2a6-4ada-8d50-a457ed6600b0" />


Now, we can observe that Kali and Windows host both are on the same NAT network and have different IPs

<img width="846" height="308" alt="изображение" src="https://github.com/user-attachments/assets/74997d8f-fae5-4935-8035-ca54bb1fa006" />


<img width="556" height="128" alt="изображение" src="https://github.com/user-attachments/assets/8205dc9e-08d2-4a4a-bbb8-c6cd43be0c44" />


Kali IP address: 192.186.100.4
Windows IP address: 192.186.100.5

### Stage 2: Set up an SSH 

#### 2.1 Enabling OpenSSH server

```
Add-WindowsCapability -Online -Name OpenSSH.Server~~~~0.0.1.0
```

<img width="743" height="126" alt="изображение" src="https://github.com/user-attachments/assets/91b93d2b-06b3-4888-a151-13d06bb416e5" />


#### 2.2 Starting Service

```
Start-Service sshd
Set-Service -Name sshd -StartupType Automatic
```

#### 2.3 Checking the service is running

<img width="574" height="113" alt="изображение" src="https://github.com/user-attachments/assets/7b93b364-c9c5-4978-bf08-c4cf86376e76" />


#### 2.4 Setting up a test user

```
net user testuser Password123! /add
```

### Stage 3: Executing attack from kali linux

#### 3.1 Creating a wordlist

First we are creating a simple wordlist 

```
TestPassword
TestPassword1
TestPassword2
TestPassword12
Password123
Password123!
```

And appending it into testwordlist.txt file 

<img width="288" height="283" alt="изображение" src="https://github.com/user-attachments/assets/f3a16a00-33ef-4daf-b54c-ef683c4c728b" />

#### 3.2 Executing hydra

```
hydra -l testuser -P ~/testwordlist.txt ssh://192.168.100.11
```

Attack was completed successfully

<img width="1442" height="250" alt="изображение" src="https://github.com/user-attachments/assets/ddffa339-b05a-4cfb-9835-6b27df520e9b" />


After checking the ElasticSearch, we can find out that event was captured successfully and triggered Wazuh rule with SID 5503

<img width="1493" height="777" alt="изображение" src="https://github.com/user-attachments/assets/6d7a10e8-4e50-4e0b-a645-5c33f5338b0e" />

After observing that rule, we can see that it triggers after unsuccessfull login attempt

<img width="1723" height="238" alt="изображение" src="https://github.com/user-attachments/assets/72956c12-a646-44ad-a7e3-2b142c73431e" />

Therefore, we can use it to write our custom rule that will detect brute-forcing based on multiple failed login attempts in short time span

Added the following rule to local_rules.xml

<img width="847" height="305" alt="изображение" src="https://github.com/user-attachments/assets/ab3f0dbe-540c-452f-9925-0b57a72165e1" />


