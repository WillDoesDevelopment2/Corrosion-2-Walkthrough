# Corrosion 2 Full Walk Through
i designed this Walk-through to document my full process including dead ends and where i got suck

## Recon of the Target Machine
Initially i used sudo arp-scan -l to find the ip address of the target machine, for me it was 10.0.2.16. Next we use nmap to view open ports, services running on them and the version if exposed. I used nmap -T4 -A -p- -sV 10.0.2.16 
![Alt text](/Images/Nmap.png?raw=true "Readme.txt")
This showed </br>
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)</br>
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))</br>
8080/tcp open  http    Apache Tomcat 9.0.53</br>

## Enumeration of Targets web services
Usually i do some manual enumeration for common files that may be left exposed such as looking for a Robots.txt or a Readme file however i decided to skip straight to enumeration...</br> 
of port 80
![Alt text](/Images/DirsearchPort80.png?raw=true "Readme.txt")

and port 8080
![Alt text](/Images/DirsearchPort8080.png?raw=true "Readme.txt")
Port 80 seems to be useless or may require different scan parameters, however port 8080 has a lot of interesting content that could be used for exploitation. Backup.zip is exposed to the internet, there is an exposed readme file that reads 
![Alt text](/Images/TomcatReadMe.png?raw=true "Readme.txt")
ps: Randy may be a log in credential</br>

## Searching for Exploits
There is a few interesting directories such as /shell and /manager. before i start looking into the Backup.zip i would like to know what kind of access we have to /manager directory for the tomcat server.
![Alt text](/Images/ManagerLogIn.png?raw=true "Readme.txt")</br>
Exciting!! it seems we have a location to put stolen credential if we can find them. Once you've tried some default passwords and attempt to log in anonymously (sorry but it most likely wont be that easy) we can shift our focus.
For now we will focus on the backup.zip.by typing into the browser http://<TargetIPAddress>:8080/backup.zip or using curl in the terminal curl http://<TargetIPAddress>:8080/backup.zip --output <file_name>
When you try to unzip this file it will show to be password protected. i would recommend completing this process in a designated directory to keep all the files tidy. I attempted to get into this zip file via fcrackzip using a simple brute force attack
![Alt text](/Images/Fcrackzip.png?raw=true "Readme.txt")
