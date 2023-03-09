# Checking the Kernel and Packages

Check kernel and package info on the system

Echo the number of kernel versions that are stores on this system into
`/root/kernel`

* Check all the kernel information `uname -a`

```bash
Linux ubuntu 5.4.0-131-generic #147-Ubuntu SMP Fri Oct 14 17:07:22 UTC
2022 x86_64 x86_64 x86_64 GNU/Linux
```

* Check for old versions of the kernel that are on the system `ls /boot/vm*`

```bash
/boot/vmlinuz  /boot/vmlinuz-5.4.0-131-generic  /boot/vmlinuz.old
```

* Echo the number of versions into `/root/kernel/` `echo 1 > /root/kernel`

* Next we will check how many packages are on the system ` dpkg -l | wc -l`

```bash
724
```

* What is the version of ssh on this system? Server and client. `dpkg -l | grep -i ssh`

```bash
ii  libssh-4:amd64                   0.9.3-2ubuntu2.2
amd64        tiny C SSH library (OpenSSL flavor)
ii  openssh-client                   1:8.2p1-4ubuntu0.5
amd64        secure shell (SSH) client, for secure access to remote
machines
ii  openssh-server                   1:8.2p1-4ubuntu0.5
amd64        secure shell (SSH) server, for secure access from remote
machines
ii  openssh-sftp-server              1:8.2p1-4ubuntu0.5
amd64        secure shell (SSH) sftp server module, for SFTP access from
remote machines
ii  ssh-import-id                    5.10-0ubuntu1
all          securely retrieve an SSH public key and install it locally
ii  sshfs                            3.6.0+repack+really2.10-0ubuntu1
amd64        filesystem client based on SSH File Transfer Protocol
```

Client is named openssh-client

```bash
ii  openssh-client                   1:8.2p1-4ubuntu0.5
amd64        secure shell (SSH) client, for secure access to remote
machines
```
server is named openssh-server

```bash
ii  openssh-server                   1:8.2p1-4ubuntu0.5
amd64        secure shell (SSH) server, for secure access from remote
machines
```
