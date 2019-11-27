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
zerun.dong@ip-10-10-0-100:~$ vmstat 1
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 1  0      0 2604780 320836 3256256    0    0     0   247    0    1 15  5 80  0  0
 0  0      0 2604196 320836 3256464    0    0     0     0 10899 16719 16  7 76  0  1
 0  0      0 2604516 320836 3256540    0    0     0    88 10675 16855 27  7 66  0  0
 0  0      0 2604360 320836 3256632    0    0     0     0 9806 15505 13  6 81  0  0
 0  0      0 2604268 320836 3256752    0    0     0     0 10487 16803 14  5 81  0  1
 0  0      0 2604036 320836 3256856    0    0     0  7876 10433 16069 25 10 63  2  0
```