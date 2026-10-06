# Corrosion 2 Full Walk Through
i designed this Walk-through to document my full process including dead ends and where i got suck

## Recon of the Target Machine
Initially i used sudo arp-scan -l to find the ip address of the target machine, for me it was 10.0.2.16. Next we use nmap to view open ports, services running on them and the version if exposed. I used nmap -T4 -A -p- -sV 10.0.2.16 
![Alt text](/Images/Nmap.png?raw=true "Readme.txt")
This showed </br>
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)</br>
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))</br>
8080/tcp open  http    Apache Tomcat 9.0.53</br>

Usually i do some manual enumeration for common files that may be left exposed such as looking for a Robots.txt or a Readme file however i decided to skip straight to enumeration of port 80
![Alt text](/Images/DirsearchPort80.png?raw=true "Readme.txt")

and port 8080
![Alt text](/Images/DirsearchPort8080.png?raw=true "Readme.txt")
