
For this section, I'm going to simulate a brute-force attack on Windows host from Kali linux, detect it, develop a rule and create an automation to notify SOC analyst about an attack

## Stage 1: Configure VirtualBox network adapters

When I firstly executed two machines, I noticed that both kali and windows shared the same IP address, so I had to give them two distinct IP addresses, so they will be able to communicate properly with each other

Therefore, I created a NAT network on VirtualBox 

<img width="1124" height="254" alt="изображение" src="https://github.com/user-attachments/assets/5689f2ec-4c65-4925-a080-f50e61694550" />

And set it as a NAT network for each VM

Kali:
<img width="818" height="282" alt="изображение" src="https://github.com/user-attachments/assets/547dac0e-390f-4a5c-8111-65c52f709d5a" />

Windows:
<img width="823" height="302" alt="изображение" src="https://github.com/user-attachments/assets/d7c4e83b-e2a6-4ada-8d50-a457ed6600b0" />
