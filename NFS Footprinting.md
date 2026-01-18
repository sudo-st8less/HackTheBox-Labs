# NFS Footprinting <br>
<mark>hook it up with a &#x2B50; if this helps!</mark> <br>
🐦: @<a href="https://x.com/st8less">**st8less**</a>
<br>

Target IP: 10.129.202.5

---
### Question 1:

Enumerate the NFS service and submit the contents of the flag.txt in the "nfs" share as the answer.

Let's start with a quick scan:

	sudo nmap -sV -sC -p 111,2049 10.129.202.5


	PORT     STATE SERVICE VERSION
	111/tcp  open  rpcbind 2-4 (RPC #100000)
	| rpcinfo: 
	|   program version    port/proto  service
	|   100000  2,3,4        111/tcp   rpcbind
	|   100000  2,3,4        111/udp   rpcbind
	|   100000  3,4          111/tcp6  rpcbind
	|   100000  3,4          111/udp6  rpcbind
	|   100003  3           2049/udp   nfs
	|   100003  3           2049/udp6  nfs
	|   100003  3,4         2049/tcp   nfs
	|   100003  3,4         2049/tcp6  nfs
	|   100005  1,2,3      37265/udp   mountd
	|   100005  1,2,3      38437/tcp   mountd
	|   100005  1,2,3      44745/tcp6  mountd
	|   100005  1,2,3      47916/udp6  mountd
	|   100021  1,3,4      38525/tcp   nlockmgr
	|   100021  1,3,4      41468/udp6  nlockmgr
	|   100021  1,3,4      41753/tcp6  nlockmgr
	|   100021  1,3,4      48064/udp   nlockmgr
	|   100227  3           2049/tcp   nfs_acl
	|   100227  3           2049/tcp6  nfs_acl
	|   100227  3           2049/udp   nfs_acl
	|_  100227  3           2049/udp6  nfs_acl
	2049/tcp open  nfs     3-4 (RPC #100003)

Found 111 and 2049 open, iterated another scan with nfs script flag:

	[★]$ sudo nmap -sV -script=nfs* -p 111,2049 10.129.202.5
	Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-08-18 22:23 CDT
	Nmap scan report for 10.129.202.5
	Host is up (0.0092s latency).
	
	PORT     STATE SERVICE VERSION
	111/tcp  open  rpcbind 2-4 (RPC #100000)
	| rpcinfo: 
	|   program version    port/proto  service
	|   100000  2,3,4        111/tcp   rpcbind
	|   100000  2,3,4        111/udp   rpcbind
	|   100000  3,4          111/tcp6  rpcbind
	|   100000  3,4          111/udp6  rpcbind
	|   100003  3           2049/udp   nfs
	|   100003  3           2049/udp6  nfs
	|   100003  3,4         2049/tcp   nfs
	|   100003  3,4         2049/tcp6  nfs
	|   100005  1,2,3      37265/udp   mountd
	|   100005  1,2,3      38437/tcp   mountd
	|   100005  1,2,3      44745/tcp6  mountd
	|   100005  1,2,3      47916/udp6  mountd
	|   100021  1,3,4      38525/tcp   nlockmgr
	|   100021  1,3,4      41468/udp6  nlockmgr
	|   100021  1,3,4      41753/tcp6  nlockmgr
	|   100021  1,3,4      48064/udp   nlockmgr
	|   100227  3           2049/tcp   nfs_acl
	|   100227  3           2049/tcp6  nfs_acl
	|   100227  3           2049/udp   nfs_acl
	|_  100227  3           2049/udp6  nfs_acl
	| nfs-ls: Volume /var/nfs
	|   access: Read Lookup Modify Extend Delete NoExecute
	| PERMISSION  UID    GID    SIZE  TIME                 FILENAME
	| rwxr-xr-x   65534  65534  4096  2021-11-08T15:08:27  .
	| ??????????  ?      ?      ?     ?                    ..
	| rw-r--r--   65534  65534  39    2021-11-08T15:08:27  flag.txt
	| 
	| 
	| Volume /mnt/nfsshare
	|   access: Read Lookup Modify Extend Delete NoExecute
	| PERMISSION  UID    GID    SIZE  TIME                 FILENAME
	| rwxr-xr-x   65534  65534  4096  2021-11-08T14:06:40  .
	| ??????????  ?      ?      ?     ?                    ..
	| rw-r--r--   65534  65534  59    2021-11-08T14:06:40  flag.txt
	|_
	| nfs-statfs: 
	|   Filesystem     1K-blocks  Used       Available  Use%  Maxfilesize  Maxlink
	|   /var/nfs       5090944.0  3256180.0  1766000.0  65%   16.0T        32000
	|_  /mnt/nfsshare  5090944.0  3256180.0  1766000.0  65%   16.0T        32000
	| nfs-showmount: 
	|   /var/nfs 10.0.0.0/8
	|_  /mnt/nfsshare 10.0.0.0/8
	2049/tcp open  nfs     3-4 (RPC #100003)

found flag.txt located on /mnt/nfsshare with read bits set for all.


locally ran:

	st8less@htb[/htb]$ mkdir target-NFS
	st8less@htb[/htb]$ sudo mount -t nfs 10.129.14.128:/ ./target-NFS/ -o nolock
	st8less@htb[/htb]$ cd target-NFS
	st8less@htb[/htb]$ tree .

Share showed up in working dir, found flag in /target-nfs/var/nfs:</br>
&#x1F6A9;**HTB{hjglmvtkjhlkfuhgi734zthrie7r--edit--**



---
### Question 2:

Enumerate the NFS service and submit the contents of the flag.txt in the "nfsshare" share as the answer.

Flag was literally in the other share folder that was sync'd with my nfs mountpoint<br>
&#x1F6A9;**HTB{8o7435zhtuih7fztdrzuhdhkfjcn7ghi4357ndcthzuc7r--edit--**

---
