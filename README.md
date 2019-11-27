# troubleshooting-and-debugging-linux
1. 通用的查看系统概况 w, 业务日志，vmstate, htop, iotop, iftop, sar -n DEV 1 df -h 查看 inodes 满
2. 查看进程状态，systemd status, lsof, ss -ant, strace， ps aux 进程 pid 1
3. 查看性能 perf, 查看网络 tcpdump，ping, dns, route ifconfig 丢包啥的
4. 单机如何混沌测试，systemtap，tc


* [overall](overall.md)
* process
* network
* performance
* chaos

![linux observability tools](images/linux_observability_tools.png)
