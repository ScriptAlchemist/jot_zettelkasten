# Look Around The Linux System

> Linux Commands to gather information

Follow along and look around a new Linux system for the first time.

* First we check what version of Linux we're on `cat /etc/*release`

```bash
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04.5 LTS"
NAME="Ubuntu"
VERSION="20.04.5 LTS (Focal Fossa)"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 20.04.5 LTS"
VERSION_ID="20.04"
HOME_URL="https://www.ubuntu.com/"
SUPPORT_URL="https://help.ubuntu.com/"
BUG_REPORT_URL="https://bugs.launchpad.net/ubuntu/"
PRIVACY_POLICY_URL="https://www.ubuntu.com/legal/terms-and-policies/privacy-policy"
VERSION_CODENAME=focal
UBUNTU_CODENAME=focal
```

`kali`:

```bash
PRETTY_NAME="Kali GNU/Linux Rolling"
NAME="Kali GNU/Linux"
VERSION="2023.1"
VERSION_ID="2023.1"
VERSION_CODENAME="kali-rolling"
ID=kali
ID_LIKE=debian
HOME_URL="https://www.kali.org/"
SUPPORT_URL="https://forums.kali.org/"
BUG_REPORT_URL="https://bugs.kali.org/"
ANSI_COLOR="1;31"
```

* Next we check the kernel version `uname -r`

```bash
5.4.0-131-generic
```

```bash
6.1.0-kali5-amd64
```

* Next we might want to know how long the system has been up `uptime`

```bash
07:28:50 up 52 min,  0 users,  load average: 0.07, 0.06, 0.05
```

```bash
02:29:18 up 31 min,  1 user,  load average: 0.80, 0.84, 0.81
```

* We might want to see how the system booted and what kernel parameters
  were passed when they system was started `cat /proc/cmdline`

```bash
BOOT_IMAGE=/boot/vmlinuz-5.4.0-131-generic root=LABEL=cloudimg-rootfs ro console=tty1 console=ttyS0
```

As always let dig deeper.

* Let's look at the virtual memory usage of this system `vmstat 1 5`

```bash
Linux 5.4.0-131-generic (ubuntu)        03/08/23  _x86_64_ (1 CPU)

07:36:29     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
07:36:30     all    1.98    9.90    0.00    0.00    0.00    0.00    0.00    0.00    0.00   88.12
07:36:31     all    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
07:36:32     all    1.01    9.09    4.04    0.00    0.00    0.00    0.00    0.00    0.00   85.86
07:36:33     all    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
07:36:34     all    0.00    0.00    1.01    0.00    0.00    0.00    0.00    0.00    0.00   98.99
Average:     all    0.60    3.82    1.00    0.00    0.00    0.00    0.00    0.00    0.00   94.58
```

What are you seeing here? Is this system under high memory usage or not?

* Check the overall CPU usage of the system every second for 5 seconds
  `mpstat 1 5`

```bash
Linux 5.4.0-131-generic (ubuntu)        03/08/23  _x86_64_ (1 CPU)

07:38:14     CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
07:38:15     all    0.00    1.00    0.00    1.00    0.00    0.00    0.00    0.00    0.00   98.00
07:38:16     all    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
07:38:17     all    0.99    0.00    0.99    0.00    0.00    0.00    0.00    0.00    0.00   98.02
07:38:18     all    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
07:38:19     all    0.00   10.00    3.00    0.00    0.00    0.00    0.00    0.00    0.00   87.00
Average:     all    0.20    2.20    0.80    0.20    0.00    0.00    0.00    0.00    0.00   96.59
```

Is this system under high CPU load or not?

* Check what processes are running on the system `ps -ef`:

```bash
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 06:36 ?        00:00:14 /sbin/init
root           2       0  0 06:36 ?        00:00:00 [kthreadd]
root           3       2  0 06:36 ?        00:00:00 [rcu_gp]
root           4       2  0 06:36 ?        00:00:00 [rcu_par_gp]
root           6       2  0 06:36 ?        00:00:00 [kworker/0:0H-events_highpri]
root           8       2  0 06:36 ?        00:00:00 [mm_percpu_wq]
root           9       2  0 06:36 ?        00:00:00 [ksoftirqd/0]
root          10       2  0 06:36 ?        00:00:00 [rcu_sched]
root          11       2  0 06:36 ?        00:00:00 [migration/0]
root          12       2  0 06:36 ?        00:00:00 [idle_inject/0]
root          13       2  0 06:36 ?        00:00:01 [kworker/0:1-events]
root          14       2  0 06:36 ?        00:00:00 [cpuhp/0]
root          15       2  0 06:36 ?        00:00:00 [kdevtmpfs]
root          16       2  0 06:36 ?        00:00:00 [netns]
root          17       2  0 06:36 ?        00:00:00 [rcu_tasks_kthre]
root          18       2  0 06:36 ?        00:00:00 [kauditd]
root          19       2  0 06:36 ?        00:00:00 [khungtaskd]
root          20       2  0 06:36 ?        00:00:00 [oom_reaper]
root          21       2  0 06:36 ?        00:00:00 [writeback]
root          22       2  0 06:36 ?        00:00:00 [kcompactd0]
root          23       2  0 06:36 ?        00:00:00 [ksmd]
root          24       2  0 06:36 ?        00:00:00 [khugepaged]
root          70       2  0 06:36 ?        00:00:00 [kintegrityd]
root          71       2  0 06:36 ?        00:00:00 [kblockd]
root          72       2  0 06:36 ?        00:00:00 [blkcg_punt_bio]
root          73       2  0 06:36 ?        00:00:00 [tpm_dev_wq]
root          74       2  0 06:36 ?        00:00:00 [ata_sff]
root          75       2  0 06:36 ?        00:00:00 [md]
root          76       2  0 06:36 ?        00:00:00 [edac-poller]
root          77       2  0 06:36 ?        00:00:00 [devfreq_wq]
root          78       2  0 06:36 ?        00:00:00 [watchdogd]
root          81       2  0 06:36 ?        00:00:00 [kswapd0]
root          82       2  0 06:36 ?        00:00:00 [ecryptfs-kthrea]
root          84       2  0 06:36 ?        00:00:00 [kthrotld]
root          85       2  0 06:36 ?        00:00:00 [irq/24-aerdrv]
root          86       2  0 06:36 ?        00:00:00 [irq/25-aerdrv]
root          87       2  0 06:36 ?        00:00:00 [irq/26-aerdrv]
root          88       2  0 06:36 ?        00:00:00 [irq/27-aerdrv]
root          89       2  0 06:36 ?        00:00:00 [irq/28-aerdrv]
root          90       2  0 06:36 ?        00:00:00 [irq/29-aerdrv]
root          91       2  0 06:36 ?        00:00:00 [acpi_thermal_pm]
root          92       2  0 06:36 ?        00:00:01 [kworker/0:1H-kblockd]
root          93       2  0 06:36 ?        00:00:00 [vfio-irqfd-clea]
root          94       2  0 06:36 ?        00:00:00 [ipv6_addrconf]
root         103       2  0 06:36 ?        00:00:00 [kstrp]
root         106       2  0 06:36 ?        00:00:00 [kworker/u3:0]
root         119       2  0 06:36 ?        00:00:00 [charger_manager]
root         158       2  0 06:36 ?        00:00:00 [scsi_eh_0]
root         159       2  0 06:36 ?        00:00:00 [scsi_tmf_0]
root         160       2  0 06:36 ?        00:00:00 [cryptd]
root         177       2  0 06:36 ?        00:00:00 [scsi_eh_1]
root         179       2  0 06:36 ?        00:00:00 [scsi_tmf_1]
root         181       2  0 06:36 ?        00:00:00 [scsi_eh_2]
root         185       2  0 06:36 ?        00:00:00 [scsi_tmf_2]
root         188       2  0 06:36 ?        00:00:00 [scsi_eh_3]
root         189       2  0 06:36 ?        00:00:00 [ttm_swap]
root         191       2  0 06:36 ?        00:00:00 [scsi_tmf_3]
root         194       2  0 06:36 ?        00:00:00 [scsi_eh_4]
root         196       2  0 06:36 ?        00:00:00 [scsi_tmf_4]
root         199       2  0 06:36 ?        00:00:00 [scsi_eh_5]
root         201       2  0 06:36 ?        00:00:00 [scsi_tmf_5]
root         203       2  0 06:36 ?        00:00:00 [scsi_eh_6]
root         205       2  0 06:36 ?        00:00:00 [scsi_tmf_6]
root         239       2  0 06:36 ?        00:00:00 [raid5wq]
root         279       2  0 06:36 ?        00:00:00 [jbd2/vda1-8]
root         280       2  0 06:36 ?        00:00:00 [ext4-rsv-conver]
root         350       1  0 06:36 ?        00:00:00 /lib/systemd/systemd-journald
root         385       1  0 06:36 ?        00:00:01 /lib/systemd/systemd-udevd
systemd+     395       1  0 06:36 ?        00:00:00 /lib/systemd/systemd-networkd
root         470       2  0 06:36 ?        00:00:00 [kaluad]
root         471       2  0 06:36 ?        00:00:00 [kmpath_rdacd]
root         472       2  0 06:36 ?        00:00:00 [kmpathd]
root         473       2  0 06:36 ?        00:00:00 [kmpath_handlerd]
root         474       1  0 06:36 ?        00:00:00 /sbin/multipathd -d -s
root         482       2  0 06:36 ?        00:00:00 [loop0]
root         485       2  0 06:36 ?        00:00:00 [loop1]
root         487       2  0 06:36 ?        00:00:00 [loop2]
root         539       1  0 06:36 ?        00:00:00 /usr/lib/accountsservice/accounts-daemon
message+     540       1  0 06:36 ?        00:00:01 /usr/bin/dbus-daemon --system --address=systemd: -
root         547       1  0 06:36 ?        00:00:00 /usr/bin/python3 /usr/bin/networkd-dispatcher --ru
root         554       1  0 06:36 ?        00:00:00 /usr/sbin/cron -f
root         555       1  0 06:36 ?        00:00:00 /usr/lib/policykit-1/polkitd --no-debug
syslog       556       1  0 06:36 ?        00:00:00 /usr/sbin/rsyslogd -n -iNONE
root         562       1  0 06:36 ?        00:00:00 /lib/systemd/systemd-logind
root         564       1  0 06:36 ?        00:00:00 /usr/lib/udisks2/udisksd
root         578       1  0 06:36 ttyS0    00:00:00 /sbin/agetty -o -p -- \u --keep-baud 115200,38400,
root         595       1  0 06:36 tty1     00:00:00 /sbin/agetty -o -p -- \u --noclear tty1 linux
root         597       1  0 06:36 ?        00:00:00 sshd: /usr/sbin/sshd -D [listener] 0 of 10-100 sta
daemon       605       1  0 06:36 ?        00:00:00 /usr/sbin/atd -f
root         612       1  0 06:36 ?        00:00:00 /usr/sbin/ModemManager
root         639       1  0 06:36 ?        00:00:00 /usr/bin/python3 /usr/share/unattended-upgrades/un
root        5930       2  0 06:38 ?        00:00:00 bpfilter_umh
root        7377       1  0 06:38 ?        00:00:00 /usr/bin/dockerd -H fd:// --containerd=/run/contai
root       13641       1  0 06:39 ?        00:00:04 /usr/bin/containerd
root       21578       1  0 06:40 ?        00:00:05 /opt/theia/node /opt/theia/browser-app/src-gen/bac
root       21589       1  0 06:40 ?        00:00:00 bash -c while true; do /bin/kc-terminal -p 40200 -
root       21591   21589  0 06:40 ?        00:00:00 /bin/kc-terminal -p 40200 -t disableLeaveAlert=tru
root       21612     597  0 06:40 ?        00:00:00 sshd: kc-internal@notty
root       21647       1  0 06:41 ?        00:00:00 bash -c export RV_SCRIPT_DIR=/var/run/kc-internal/
root       21650   21647  0 06:41 ?        00:00:00 /bin/runtime-scenario-service
root       21668       1  0 06:41 ?        00:00:00 dhclient -v
root       21720       1  0 06:41 ?        00:00:00 dhclient -v
root       21753       1  0 06:41 ?        00:00:00 dhclient -v
systemd+   21791       1  0 06:41 ?        00:00:00 /lib/systemd/systemd-timesyncd
root       21891       1  0 06:41 ?        00:00:00 /usr/libexec/fwupd/fwupd
root       21917       1  0 06:41 ?        00:00:00 /bin/runtime-info-service
root       21932       1  0 06:41 ?        00:00:00 gpg-agent --homedir /var/lib/fwupd/gnupg --use-sta
root       23080   21591  0 07:08 pts/0    00:00:00 bash
root       23087   21578  0 07:08 ?        00:00:08 /opt/theia/node /opt/theia/node_modules/@theia/cor
root       23111   21578  0 07:08 ?        00:00:00 /opt/theia/node /opt/theia/node_modules/@theia/plu
root       23113   21578  0 07:08 pts/1    00:00:00 /bin/bash
root       24209       2  0 07:17 ?        00:00:00 [kworker/u2:0-events_unbound]
root       24927       2  0 07:25 ?        00:00:00 [kworker/u2:2-events_power_efficient]
root       25009       2  0 07:26 ?        00:00:00 [kworker/0:0-events]
root       25553       2  0 07:35 ?        00:00:00 [kworker/u2:1-events_unbound]
root       25804       2  0 07:37 ?        00:00:00 [kworker/u2:3]
root       25821       2  0 07:37 ?        00:00:00 [kworker/0:2-events]
systemd+   26105       1  2 07:39 ?        00:00:00 /lib/systemd/systemd-resolved
root       26106     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26107     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26108     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26109     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26110     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26111     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26112     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26113     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26114     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26115     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26116     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26117     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26118     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26119     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26120     385  0 07:39 ?        00:00:00 /lib/systemd/systemd-udevd
root       26134   23080  0 07:39 pts/0    00:00:00 ps -ef
```

* Now run `ps -ef | awk '{print $1}' | sort | uniq -c`

```bash
      1 UID
      1 daemon
      1 message+
    131 root
      1 syslog
      3 systemd+
```

What users is using the most processes? Do you think this system is
doing any real work or just sitting there running an OS?

Next we check what processes are executing on every second `pidstat 1 5`

```bash
Linux 5.4.0-131-generic (ubuntu)        03/08/23  _x86_64_ (1 CPU)

07:48:26      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
07:48:27        0     26927    0.98    1.96    0.00    0.00    2.94     0  pidstat

07:48:27      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
07:48:28        0     21591    0.00    1.00    0.00    0.00    1.00     0  kc-terminal
07:48:28        0     26927    0.00    1.00    0.00    0.00    1.00     0  pidstat

07:48:28      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
07:48:29        0     23087    0.00    1.00    0.00    0.00    1.00     0  node

07:48:29      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
07:48:30        0     21578    0.00    1.00    0.00    0.00    1.00     0  node
07:48:30        0     26927    1.00    1.00    0.00    0.00    2.00     0  pidstat

07:48:30      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
07:48:31        0     21591    0.00    1.00    0.00    0.00    1.00     0  kc-terminal
07:48:31        0     23087    1.00    0.00    0.00    0.00    1.00     0  node
07:48:31        0     26927    0.00    1.00    0.00    0.00    1.00     0  pidstat

Average:      UID       PID    %usr %system  %guest   %wait    %CPU   CPU  Command
Average:        0     21578    0.00    0.20    0.00    0.60    0.20     -  node
Average:        0     21591    0.00    0.40    0.00    0.00    0.40     -  kc-terminal
Average:        0     23087    0.20    0.20    0.00    0.00    0.40     -  node
Average:        0     26927    0.40    1.00    0.00    0.20    1.39     -  pidstat
```

Why do these have different length output? What processes were using the
most CPU? Which showed up the most often?

Next we may want to see more CPU and Disk usage on the system in 1
second increments. Do you think you could modify this to run for 30
seconds? `iostat -xz 1 5`

```bash
Linux 5.4.0-131-generic (ubuntu)        03/08/23  _x86_64_ (1 CPU)

avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           2.40    1.29    1.95    0.68    0.23   93.45

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz  aqu-sz  %util
loop0            0.39      0.45     0.00   0.00    1.08     1.17    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00   0.01
loop1            0.02      0.24     0.00   0.00    0.65    14.22    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00   0.00
loop2            0.01      0.08     0.00   0.00    1.00     5.97    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00   0.00
loop3            0.00      0.00     0.00   0.00    0.00     1.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00      0.00     0.00   0.00    0.00     0.00    0.00   0.00
vda              4.59    126.36     1.08  19.02    1.67    27.54    8.53    783.82    18.41  68.33   12.89    91.88    0.21   4390.38     0.00   0.00    0.50 20550.60    0.10   1.54


avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.00    0.00    1.98    0.00    0.00   98.02

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz  aqu-sz  %util


avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.00    0.00    0.00    0.00    0.00  100.00

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz  aqu-sz  %util


avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.99    0.99    0.00    0.00    0.00   98.02

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz  aqu-sz  %util


avg-cpu:  %user   %nice %system %iowait  %steal   %idle
           0.00    0.00    1.00    0.00    0.00   99.00

Device            r/s     rkB/s   rrqm/s  %rrqm r_await rareq-sz     w/s     wkB/s   wrqm/s  %wrqm w_await wareq-sz     d/s     dkB/s   drqm/s  %drqm d_await dareq-sz  aqu-sz  %util
```

What about some networking?

* Let's look at the network usage and load of the system. `sar -n DEV 1
  5`

```bash
Linux 5.4.0-131-generic (ubuntu)        03/08/23  _x86_64_ (1 CPU)

07:55:06        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
07:55:07      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:55:07       enp1s0      2.00      1.00      0.13      0.24      0.00      0.00      0.00      0.00
07:55:07           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

07:55:07        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
07:55:08      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:55:08       enp1s0      5.00      5.00      0.32      1.28      0.00      0.00      0.00      0.00
07:55:08           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

07:55:08        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
07:55:09      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:55:09       enp1s0      7.00      7.00      0.46      1.27      0.00      0.00      0.00      0.00
07:55:09           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

07:55:09        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
07:55:10      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:55:10       enp1s0      4.00      4.00      0.26      0.99      0.00      0.00      0.00      0.00
07:55:10           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

07:55:10        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
07:55:11      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
07:55:11       enp1s0      6.00      6.00      0.39      1.51      0.00      0.00      0.00      0.00
07:55:11           lo      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00

Average:        IFACE   rxpck/s   txpck/s    rxkB/s    txkB/s   rxcmp/s   txcmp/s  rxmcst/s   %ifutil
Average:      docker0      0.00      0.00      0.00      0.00      0.00      0.00      0.00      0.00
Average:       enp1s0      4.80      4.60      0.31      1.06      0.00      0.00      0.00      0.00
Average:           lo      0.00      0.00      0.00      0.00      0.00
0.00      0.00      0.00
```

What are you seeing here? What devices are showing up? Do any devices
seem to be under high load? Which one has the most traffic?

* Check tcp packets and errors `sar -n TCP,ETCP 1 5`

```bash
Linux 5.4.0-131-generic (ubuntu)        03/08/23  _x86_64_ (1 CPU)

07:57:12     active/s passive/s    iseg/s    oseg/s
07:57:13         0.00      0.00      2.00      0.00

07:57:12     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
07:57:13         0.00      0.00      0.00      0.00      0.00

07:57:13     active/s passive/s    iseg/s    oseg/s
07:57:14         0.00      0.00      4.00      5.00

07:57:13     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
07:57:14         0.00      0.00      0.00      0.00      0.00

07:57:14     active/s passive/s    iseg/s    oseg/s
07:57:15         0.00      0.00      3.00      3.00

07:57:14     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
07:57:15         0.00      0.00      0.00      0.00      0.00

07:57:15     active/s passive/s    iseg/s    oseg/s
07:57:16         0.00      0.00      3.00      3.00

07:57:15     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
07:57:16         0.00      0.00      0.00      0.00      0.00

07:57:16     active/s passive/s    iseg/s    oseg/s
07:57:17         0.00      0.00      3.00      3.00

07:57:16     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
07:57:17         0.00      0.00      0.00      0.00      0.00

Average:     active/s passive/s    iseg/s    oseg/s
Average:         0.00      0.00      3.00      2.80

Average:     atmptf/s  estres/s retrans/s isegerr/s   orsts/s
Average:         0.00      0.00      0.00      0.00      0.00
```

Do we appear to be seeing any large numbers of errors? Why might
retransmits be a big problem?


