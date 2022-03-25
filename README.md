# Startup WriteUp (Tryhackme) by [yag1n3](https://github.com/yaguine)

### flags
1. What is the secret spicy soup recipe?
2. What are the contents of user.txt?
3. What are the contents of root.txt?

### labels
* wireshark
* cron
* gobuster
* enumeration

---

## First Steps

the ICMP TTL tells us that this a Linux Machine

### nmap 
* p21: vsftpd 3.0.3
* p22: OpenSSH 7.2p2 Ubuntu 4ubuntu2.10
* p80: Apache httpd 2.4.18

we see that the FTP server allows Anonymous login  
we extract *important.jpg* and *notice.txt* from it

notice.txt:  
Whoever is leaving these damn Among Us memes in this share, it IS NOT FUNNY. People downloading documents from our website will think we are a joke! Now I dont know who it is, but Maya is looking pretty sus.

and *important.jpg* is the Among Us meme  
now we know that **Maya** might be a username

### gobuster
we find /files, which is the folder on the FTP server share  
that means that if we upload a reverse shell php payload we might be able to have RCE

we are able to upload a webshell using the following command :  
`put <local-path>/reverse_shell.php <remote_name>`  
it's worth nothing that you have to specify a remote name to make it work  
we have the reverse shell !!!!

---

## Flag 1 : What is the secret spicy soup recipe?

we use the following command  
`find / -iname "*recipe*" 2>/dev/null`  
there is a file called */recipe.txt*  
the secret recipe is Love !!!

---

## Flag 2 : What are the contents of user.txt?

finding files owned by www-data, i encountered */incidents/suspicious.pcapng*  
files with this extension contain packets captured by **Wireshark**  
i set up an http server with python on the target and download the file on my machine with curl  
using strings on it, i found the password "c4ntg3t3n0ughsp1c3" that works with the user lennie  
we find the **user flag**

---

## Flag 3 : What are the contents of root.txt?

inside lennie's home directory there is a folder called *scripts*  
there is a script called *planner.sh* that is owned by root and that uses a env variable called $LIST  
i notice that $LIST is empty for our user, so maybe that means that root is running this script periodically  

this script also uses */etc/print.sh*, that is a script owned by us that we can actually modify  
we modify it to copy */root/root.txt* into lennie's home directory and it works !!!!!

---

## Conclusions 

This is a beginner-level room that shouldn't be a challenge at all if you have already done 20 + machines  
The part of reading the Wireshark Packets logs is interesting but still really easy to figure out
