## overall
Know the current system status at a glance

#### w - Show who is logged on and what they are doing
```shell
zerun.dong@ip-xx-xx-xx-xx:~$ w
 09:34:51 up 29 days, 11:39,  2 users,  load average: 1.29, 1.29, 1.22
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
zerun.do pts/0    xx-xx-xx-xx     03:38    1:24   0.06s  0.00s sshd: zerun.dong [priv]
zerun.do pts/1    xx-xx-xx-xx     09:30    1.00s  0.02s  0.00s w
```

#### vmstat - Report virtual memory statistics
pay attention to the `st` field
```shell
zerun.dong@ip-xx-xx-xx-xx:~$ vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 2604780 320836 3256256    0    0     0   247    0    1 15  5 80  0  0
 0  0      0 2604196 320836 3256464    0    0     0     0 10899 16719 16  7 76  0  1
 0  0      0 2604516 320836 3256540    0    0     0    88 10675 16855 27  7 66  0  0
 0  0      0 2604360 320836 3256632    0    0     0     0 9806 15505 13  6 81  0  0
 0  0      0 2604268 320836 3256752    0    0     0     0 10487 16803 14  5 81  0  1
 0  0      0 2604036 320836 3256856    0    0     0  7876 10433 16069 25 10 63  2  0
```

#### top&htop - display Linux processes
```shell
zerun.dong@ip-xx-xx-xx-xx:~$ top
top - 09:45:29 up 29 days, 11:50,  2 users,  load average: 0.56, 0.75, 0.97
Tasks: 121 total,   1 running,  79 sleeping,   0 stopped,   0 zombie
%Cpu(s): 20.5 us,  7.3 sy,  0.0 ni, 70.6 id,  0.2 wa,  0.0 hi,  1.2 si,  0.3 st
KiB Mem :  8166936 total,  3133876 free,  2005792 used,  3027268 buff/cache
KiB Swap:        0 total,        0 free,        0 used.  6767832 avail Mem

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
27057 grabtaxi  20   0 2530724 1.262g  47928 S  27.7 16.2  92:18.27 latest
25978 root      20   0 1222376 194084  20332 S  11.0  2.4   2312:38 filebeat
12275 dd-agent  20   0 1374036 117140  55728 S   8.0  1.4   1623:33 agent
18626 root      19  -1  172852  42344  29172 S   5.3  0.5   1123:13 systemd-journal
27359 telegraf  20   0  982912 140704  39972 S   4.3  1.7   6:12.10 telegraf
  763 syslog    20   0  267340   5088   3316 S   3.0  0.1   1:09.16 rsyslogd
12276 dd-agent  20   0  884240  42736  25988 S   1.0  0.5  66:30.50 process-agent
    7 root      20   0       0      0      0 S   0.3  0.0   7:38.23 ksoftirqd/0
13141 root      20   0  277132  35808   7780 S   0.3  0.4   1:38.08 nessusd
    1 root      20   0  160016   9708   7072 S   0.0  0.1   2:13.71 systemd
```
![htop](images/htop.jpg)