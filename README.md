# Corrosion 2 VulnHub Vulnerable Machine Walk-through

Platform: VulnHub Status: Completed Difficulty: Easy Medium</br>
Completed: October 2026</br>
A vulnerable machine practice focusing on web application reconnaissance, archive password cracking, Tomcat WAR deployment for initial access, credential reuse, and Linux privilege escalation via Python library hijacking.
## Machine Information

Machine Name: Corrosion 2</br>
Release Date: 2021</br>
Target IP In my Documentation: 10.0.2.16</br>
Completion Date: October 5, 2026</br>
Time to Complete: Approximately X hours</br>
Difficulty Rating: Easy Medium

## Tools Used

Nmap: Port scanning and service enumeration</br>
Dirsearch: Web directory brute-forcing</br>
fcrackzip / John the Ripper (zip2john): Archive password cracking</br>
MSFVenom: WAR reverse shell payload generation</br>
Netcat: Reverse shell listener and shadow file transfer</br>
John the Ripper: Shadow hash cracking</br>
Nano / Vim: Payload and malicious library creation</br>
Python: Shell upgrade and privilege escalation

## Vulnerabilities Exploited

Password-Protected Backup Archive: backup.zip crackable with rockyou wordlist</br>
Exposed Tomcat Credentials: Plaintext credentials in tomcat-user.xml inside the archive</br>
Reused Credentials: Tomcat admin password reused for SSH access</br>
SUID Misconfiguration: Custom binary with root privileges exposing /etc/shadow</br>
Weak User Passwords: Randy's shadow hash cracked via dictionary attack</br>
Insecure Sudo Configuration: Python script executed as root with writable library dependency</br>
Python Library Hijacking: Replaceable standard library file enabling root code execution</br>

## Attack Path Summary

Initial reconnaissance with Nmap identified open ports (22, 80, 8080) running SSH, Apache 2.4.41, and Apache Tomcat 9.0.53
Web enumeration via Dirsearch on port 8080 discovered /backup.zip, /manager, and /shell directories</br>
Backup archive password cracked using fcrackzip and zip2john with the rockyou wordlist</br>
tomcat_user.xml inside the archive revealed Tomcat manager credentials</br>
Reverse shell WAR file crafted with MSFVenom (or manually packaged JSP payload) and deployed via Tomcat manager</br>
Netcat listener established shell, upgraded with a PTY spawn</br>
user.txt flag recovered from randy's home directory</br>
SSH access gained as jaye reusing the Tomcat admin password</br>
SUID/GUID binary look used to extract /etc/shadow with root privileges and transferred to attacker machine via netcat</br>
Randy's password cracked from shadow hashes using John the Ripper</br>
sudo -l revealed randy can execute /home/randy/randombase64.py as root via Python 3.8</br>
Python library hijacking performed by replacing /usr/lib/python3.8/base64.py with malicious code</br>
Script executed with sudo granted root shell</br>
root.txt flag captured

## Repository Contents

walkthrough.md: Step-by-step methodology with commands</br>
lessons-learned.md: Where I got stuck and recovery strategies</br>
screenshots/: Proof of access with sensitive data redacted

## Skills Demonstrated

Network reconnaissance and service fingerprinting</br>
Web application security assessment</br>
Archive password brute-forcing</br>
Tomcat exploitation via WAR file deployment</br>
Manual and automated reverse shell creation</br>
Shell stabilization and post-exploitation</br>
Credential reuse analysis</br>
Hash extraction and offline cracking</br>
File transfer between attacker and target machines</br>
Sudo misconfiguration analysis</br>
Privilege escalation via Python library hijacking</br>
Technical documentation and reporting
## Disclaimer

This machine is from VulnHub and intended for educational purposes only. All techniques were performed in a locally controlled authorized environment using a VirtualBox VM at 10.0.2.16. Unauthorized testing on production systems is illegal and unethical.

This walkthrough respects the VulnHub community by intentionally omitting full flags and sensitive credentials in linked files to preserve learning value for other learners.
Contact

GitHub: @willdoesdevelopment2</br>
Email: JulianDelphinki01@pm.me</br>
Last Updated: October 5, 2026
