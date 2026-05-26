
For this section, I'm going to simulate a brute-force attack on Windows host from Kali linux, detect it, develop a rule and create an automation to notify SOC analyst about an attack

## Stage 1: Configure VirtualBox network adapters

When I firstly executed two machines, I noticed that both kali and windows shared the same IP address, so I had to give them two distinct IP addresses, so they will be able to communicate properly with each other
