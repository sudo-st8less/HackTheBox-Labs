# &#x1F6A9; Footprinting Lab - Medium <br>
<mark>Hack The Box Penetration Tester Path full walkthrough <a href="https://github.com/sudo-st8less?tab=repositories">catalog</a>.</mark><br>
### <mark>hook the repo up with a &#x2B50; if it helps</mark>
### 🐦: @<a href="https://x.com/st8less">**st8less**</a>

---

Target IP:
10.129.202.41

Hint:
In SQL Management Studio, we can edit the last 200 entries of the selected database and read the entries accordingly. We also need to keep in mind, that each Windows system has an Administrator account.

- InlaneFreight Ltd
- inspect DNS server, do not exploit
- given creds:       **ceil:qwer1234** 
- SSH in use
- found creds:    **alex:lol123!mD**
- found creds on windows:  **sa:87N1ns@slls83**

Engagement:<br>
This second server is a server that everyone on the internal network has access to. In our discussion with our client, we pointed out that these servers are often one of the main targets for attackers and that this server should be added to the scope.

Our customer agreed to this and added this server to our scope. Here, too, the goal remains the same. We need to find out as much information as possible about this server and find ways to use it against the server itself. For the proof and protection of customer data, a user named `HTB` has been created. Accordingly, we need to obtain the credentials of this user as proof.

---

### Question 1:
#### Enumerate the server carefully and find the username "HTB" and its password. Then, submit this user's password as the answer.

Start with a scan of all ports:

	$ sudo nmap -sT -sV -sC -O -A -oA init_scan.txt -p- -v 10.129.202.41

	PORT      STATE SERVICE       VERSION
	111/tcp   open  rpcbind       2-4 (RPC #100000)
	| rpcinfo: 
	|   program version    port/proto  service
	|   100000  2,3,4        111/tcp   rpcbind
	|   100000  2,3,4        111/tcp6  rpcbind
	|   100000  2,3,4        111/udp   rpcbind
	|   100000  2,3,4        111/udp6  rpcbind
	|   100003  2,3         2049/udp   nfs
	|   100003  2,3         2049/udp6  nfs
	|   100003  2,3,4       2049/tcp   nfs
	|   100003  2,3,4       2049/tcp6  nfs
	|   100005  1,2,3       2049/tcp   mountd
	|   100005  1,2,3       2049/tcp6  mountd
	|   100005  1,2,3       2049/udp   mountd
	|   100005  1,2,3       2049/udp6  mountd
	|   100021  1,2,3,4     2049/tcp   nlockmgr
	|   100021  1,2,3,4     2049/tcp6  nlockmgr
	|   100021  1,2,3,4     2049/udp   nlockmgr
	|   100021  1,2,3,4     2049/udp6  nlockmgr
	|   100024  1           2049/tcp   status
	|   100024  1           2049/tcp6  status
	|   100024  1           2049/udp   status
	|_  100024  1           2049/udp6  status
	135/tcp   open  msrpc         Microsoft Windows RPC
	139/tcp   open  netbios-ssn   Microsoft Windows netbios-ssn
	445/tcp   open  microsoft-ds?
	2049/tcp  open  nlockmgr      1-4 (RPC #100021)
	3389/tcp  open  ms-wbt-server Microsoft Terminal Services
	| rdp-ntlm-info: 
	|   Target_Name: WINMEDIUM
	|   NetBIOS_Domain_Name: WINMEDIUM
	|   NetBIOS_Computer_Name: WINMEDIUM
	|   DNS_Domain_Name: WINMEDIUM
	|   DNS_Computer_Name: WINMEDIUM
	|   Product_Version: 10.0.17763
	|_  System_Time: 2025-08-28T23:02:20+00:00
	| ssl-cert: Subject: commonName=WINMEDIUM
	| Issuer: commonName=WINMEDIUM
	| Public Key type: rsa
	| Public Key bits: 2048
	| Signature Algorithm: sha256WithRSAEncryption
	| Not valid before: 2025-08-27T19:58:39
	| Not valid after:  2026-02-26T19:58:39
	| MD5:   0a97:411c:d272:d5bc:67a3:cde1:9ec4:a4fb
	|_SHA-1: e045:509d:61a9:bacf:0ce9:7b90:78be:e61b:998c:e031
	|_ssl-date: 2025-08-28T23:02:28+00:00; 0s from scanner time.
	5985/tcp  open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
	|_http-server-header: Microsoft-HTTPAPI/2.0
	|_http-title: Not Found
	47001/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
	|_http-server-header: Microsoft-HTTPAPI/2.0
	|_http-title: Not Found
	49664/tcp open  msrpc         Microsoft Windows RPC
	49665/tcp open  msrpc         Microsoft Windows RPC
	49666/tcp open  msrpc         Microsoft Windows RPC
	49667/tcp open  msrpc         Microsoft Windows RPC
	49668/tcp open  msrpc         Microsoft Windows RPC
	49679/tcp open  msrpc         Microsoft Windows RPC
	49680/tcp open  msrpc         Microsoft Windows RPC
	49681/tcp open  msrpc         Microsoft Windows RPC
	No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
	TCP/IP fingerprint:
	OS:SCAN(V=7.94SVN%E=4%D=8/28%OT=111%CT=1%CU=42061%PV=Y%DS=2%DC=T%G=Y%TM=68B
	OS:0E00A%P=x86_64-pc-linux-gnu)SEQ(SP=F8%GCD=1%ISR=100%TI=I%CI=I%TS=U)SEQ(S
	OS:P=F8%GCD=1%ISR=100%TI=I%CI=I%II=I%SS=S%TS=U)OPS(O1=M552NW8NNS%O2=M552NW8
	OS:NNS%O3=M552NW8%O4=M552NW8NNS%O5=M552NW8NNS%O6=M552NNS)WIN(W1=FFFF%W2=FFF
	OS:F%W3=FFFF%W4=FFFF%W5=FFFF%W6=FF70)ECN(R=Y%DF=Y%T=80%W=FFFF%O=M552NW8NNS%
	OS:CC=Y%Q=)T1(R=Y%DF=Y%T=80%S=O%A=S+%F=AS%RD=0%Q=)T2(R=Y%DF=Y%T=80%W=0%S=Z%
	OS:A=S%F=AR%O=%RD=0%Q=)T3(R=Y%DF=Y%T=80%W=0%S=Z%A=O%F=AR%O=%RD=0%Q=)T4(R=Y%
	OS:DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=80%W=0%S=Z%A=S+%F=AR%
	OS:O=%RD=0%Q=)T6(R=Y%DF=Y%T=80%W=0%S=A%A=O%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=80%
	OS:W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=80%IPL=164%UN=0%RIPL=G%RID=G%
	OS:RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=80%CD=Z)
	
	Network Distance: 2 hops
	TCP Sequence Prediction: Difficulty=248 (Good luck!)
	IP ID Sequence Generation: Incremental
	Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
	
	Host script results:
	| smb2-time: 
	|   date: 2025-08-28T23:02:24
	|_  start_date: N/A
	| smb2-security-mode: 
	|   3:1:1: 
	|_    Message signing enabled but not required


Found these services running:

	139 - tcp - netbios
	445 - tcp - smb
	135 - tcp - rpc endpoint mapper
	111 - tcp/udp - rpc port mapper
	3389 - tcp - rdp 
	2049 - tcp/udp - NFS
	5985 - tcp - winRM http comms
	47001
	49679
	49664
	49665
	49666
	49667
	49668
	49680 

udp scan:

		111/udp  open          rpcbind     2-4 (RPC #100000)
			| rpcinfo: 
			|   program version    port/proto  service
			|   100000  2,3,4        111/tcp   rpcbind
			|   100000  2,3,4        111/tcp6  rpcbind
			|   100000  2,3,4        111/udp   rpcbind
			|   100000  2,3,4        111/udp6  rpcbind
			|   100003  2,3         2049/udp   nfs
			|   100003  2,3         2049/udp6  nfs
			|   100003  2,3,4       2049/tcp   nfs
			|   100003  2,3,4       2049/tcp6  nfs
			|   100005  1,2,3       2049/tcp   mountd
			|   100005  1,2,3       2049/tcp6  mountd
			|   100005  1,2,3       2049/udp   mountd
			|   100005  1,2,3       2049/udp6  mountd
			|   100021  1,2,3,4     2049/tcp   nlockmgr
			|   100021  1,2,3,4     2049/tcp6  nlockmgr
			|   100021  1,2,3,4     2049/udp   nlockmgr
			|   100021  1,2,3,4     2049/udp6  nlockmgr
			|   100024  1           2049/tcp   status
			|   100024  1           2049/tcp6  status
			|   100024  1           2049/udp   status
			|_  100024  1           2049/udp6  status
			123/udp  open|filtered ntp
			137/udp  open|filtered netbios-ns
			138/udp  open|filtered netbios-dgm
			500/udp  open|filtered isakmp
			1027/udp open|filtered unknown
			1030/udp open|filtered iad1
			1900/udp open|filtered upnp
			2049/udp open          nlockmgr    1-4 (RPC #100021)
			4500/udp open|filtered nat-t-ike
			5353/udp open|filtered zeroconf

Let's start with nfs:

	$ nmap -A -p 2049 10.129.202.41

Aggressive OS guesses: Microsoft Windows Server 2019 (96%)

	$ showmount -e 10.129.202.41
	
		Export list for 10.129.202.41:
		/TechSupport (everyone)

let's mount the share:

	$ mkdir targetnfs

	$ sudo mount -t nfs 10.129.202.41:/ ./targetnfs -o nolock

Tried logging in as nobody user on deb to match perms, no luck.
Decided to try as root, worked:

	nobody@htb-i9gjaur4g4:/home/htb-ac-830862/targetnfs$ sudo su
		┌─[root@htb-i9gjaur4g4]─[/home/htb-ac-830862/targetnfs]
		└──╼ #ls
		TechSupport
		┌─[root@htb-i9gjaur4g4]─[/home/htb-ac-830862/targetnfs]
		└──╼ #cd TechSupport
		┌─[root@htb-i9gjaur4g4]─[/home/htb-ac-830862/targetnfs/TechSupport]
		└──╼ #ls
		ticket4238791283649.txt  ticket4238791283688.txt  ticket4238791283727.txt  ticket4238791283766.txt
		ticket4238791283650.txt  ticket4238791283689.txt  ticket4238791283728.txt  ticket4238791283767.txt
		ticket4238791283651.txt  ticket4238791283690.txt  ticket4238791283729.txt  ticket4238791283768.txt
		ticket4238791283652.txt  ticket4238791283691.txt  ticket4238791283730.txt  ticket4238791283769.txt
		ticket4238791283653.txt  ticket4238791283692.txt  ticket4238791283731.txt  ticket4238791283770.txt
		ticket4238791283654.txt  ticket4238791283693.txt  ticket4238791283732.txt  ticket4238791283771.txt
		ticket4238791283655.txt  ticket4238791283694.txt  ticket4238791283733.txt  ticket4238791283772.txt
		ticket4238791283656.txt  ticket4238791283695.txt  ticket4238791283734.txt  ticket4238791283773.txt
		ticket4238791283657.txt  ticket4238791283696.txt  ticket4238791283735.txt  ticket4238791283774.txt
		ticket4238791283658.txt  ticket4238791283697.txt  ticket4238791283736.txt  ticket4238791283775.txt
		ticket4238791283659.txt  ticket4238791283698.txt  ticket4238791283737.txt  ticket4238791283776.txt
		ticket4238791283660.txt  ticket4238791283699.txt  ticket4238791283738.txt  ticket4238791283777.txt
		ticket4238791283661.txt  ticket4238791283700.txt  ticket4238791283739.txt  ticket4238791283778.txt
		ticket4238791283662.txt  ticket4238791283701.txt  ticket4238791283740.txt  ticket4238791283779.txt
	
	--SNIP--

got into folder but perms denies writing. found this ticket is the only one with data:

	-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283781.txt
	-rwx------ 1 nobody nogroup  1305 Nov 10  2021 ticket4238791283782.txt
	-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283783.txt
	-rwx------ 1 nobody nogroup     0 Nov 10  2021 ticket4238791283784.txt

Concat:

		#  cat ticket4238791283782.txt
		
			Conversation with InlaneFreight Ltd
			
			Started on November 10, 2021 at 01:27 PM London time GMT (GMT+0200)
			---
			01:27 PM | Operator: Hello,. 
			 
			So what brings you here today?
			01:27 PM | alex: hello
			01:27 PM | Operator: Hey alex!
			01:27 PM | Operator: What do you need help with?
			01:36 PM | alex: I run into an issue with the web config file on the system for the smtp server. do you mind to take a look at the config?
			01:38 PM | Operator: Of course
			01:42 PM | alex: here it is:
			
			 1smtp {
			 2    host=smtp.web.dev.inlanefreight.htb
			 3    #port=25
			 4    ssl=true
			 5    user="alex"
			 6    password="lol123!mD"
			 7    from="alex.g@web.dev.inlanefreight.htb"
			 8}
			 9
			10securesocial {
			11    
			12    onLoginGoTo=/
			13    onLogoutGoTo=/login
			14    ssl=false
			15    
			16    userpass {      
			17    	withUserNameSupport=false
			18    	sendWelcomeEmail=true
			19    	enableGravatarSupport=true
			20    	signupSkipLogin=true
			21    	tokenDuration=60
			22    	tokenDeleteInterval=5
			23    	minimumPasswordLength=8
			24    	enableTokenJob=true
			25    	hasher=bcrypt
			26	}
			27
			28     cookie {
			29     #       name=id
			30     #       path=/login
			31     #       domain="10.129.2.59:9500"
			32            httpOnly=true
			33            makeTransient=false
			34            absoluteTimeoutInMinutes=1440
			35            idleTimeoutInMinutes=1440
			36    }   


### found smtp creds:

host=smtp.web.dev.inlanefreight.htb <br>
port=25 <br>
ssl=true <br>
user="alex" <br>
password="lol123!mD" <br>
from="alex.g@web.dev.inlanefreight.htb" <br>

### found potential cookie:

	cookie {
	#       name=id
	#       path=/login
	#       domain="10.129.2.59:9500"
	httpOnly=true
	makeTransient=false
	absoluteTimeoutInMinutes=1440
	 idleTimeoutInMinutes=1440


Ran crackmapexec, found creds are used for SMB:

	$ crackmapexec smb 10.129.202.41 -u alex -p 'lol123!mD'
	[*] First time use detected
	[*] Creating home directory structure
	[*] Creating missing folder logs
	[*] Creating missing folder modules
	[*] Creating missing folder protocols
	[*] Creating missing folder workspaces
	[*] Creating missing folder obfuscated_scripts
	[*] Creating missing folder screenshots
	[*] Creating default workspace
	[*] Initializing MSSQL protocol database
	[*] Initializing WINRM protocol database
	[*] Initializing LDAP protocol database
	[*] Initializing SMB protocol database
	[*] Initializing SSH protocol database
	[*] Initializing VNC protocol database
	[*] Initializing WMI protocol database
	[*] Initializing FTP protocol database
	[*] Initializing RDP protocol database
	[*] Copying default configuration file
	SMB         10.129.202.41   445    WINMEDIUM        [*] Windows 10 / Server 2019 Build 17763 x64 (name:WINMEDIUM) (domain:WINMEDIUM) (signing:False) (SMBv1:False)
	SMB         10.129.202.41   445    WINMEDIUM        [+] WINMEDIUM\alex:lol123!mD 


Let's try the RDP port, make sure not to run as root:

	$ xfreerdp /v:10.129.202.41 /u:alex /p:'lol123!mD'

this opens an rdp window to a windows host. checked the user's folders and found new creds in the Devshare folder:

### found: 
**sa:87N1ns@slls83**


Access denied to SQL server auth under current accout, rdp'd into the Admin creds I found instead:

	$ xfreerdp /v:10.129.202.41 /u:Administrator /p:'87N1ns@slls83'


this logged me in as admin in windows. Opened SQL server manager. searched through dirs until i found the Sql table **dbo.devsacc**. Opened the top 200 accounts and queried for 'HTB'.  

### &#x1F6A9; found flag:
**lnch7ehrdn43i7AoqVPK4zWR**
