# Corrosion 2 Full Walk Through
i designed this Walk-through to document my full process including dead ends and where i got suck

## Recon of the Target Machine
Initially i used sudo arp-scan -l to find the ip address of the target machine, for me it was 10.0.2.16. Next we use nmap to view open ports, services running on them and the version if exposed. I used nmap -T4 -A -p- -sV 10.0.2.16 This showed </br>
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)</br>
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))</br>
8080/tcp open  http    Apache Tomcat 9.0.53</br>

![Alt text](/Images/Nmap.png?raw=true "Readme.txt")

