## Why chaos engineering useful
Chaos engineering can be used to achieve resilience against:
* Infrastructure failures
* Network failures
* Application failures

Alibaba has a toolkit [Chaosblade](https://github.com/chaosblade-io/chaosblade) easy to use and powerful chaos engineering experiment 

### Disk io latency
We use [systemtap](https://sourceware.org/systemtap/) to simulate io latency
1. write the systemtap file
```systemtap
global inject, ka_cnt

probe procfs("cnt").read {
  $value = sprintf("%d\n", ka_cnt);
}
probe procfs("inject").write {
  inject= $value;
  printf("inject count %d, ka %s", ka_cnt, inject);
}

probe vfs.read.return,
      vfs.write.return {
  if (@defined($file->f_path->dentry)) {
    dev_nr=$file->f_path->dentry->d_inode->i_sb->s_dev
    inode_nr = $file->f_path->dentry->d_inode->i_ino
    } else {
    dev_nr=$file->f_dentry->d_inode->i_sb->s_dev
    inode_nr = $file->f_dentry->d_inode->i_ino
    }

  if ($return &&
      dev_nr == MKDEV($1,$2) &&
      inject == "on\n")
  {
#   printf("dev %x func: %s\n", dev_nr, ppfunc())
    ka_cnt++;
    udelay($3);
  }
}

probe begin{
  println("ik module begin:)");
}
```
2. run `inject_ka.stp`, passed three parameters: `disk major id`, `disk minor id` and `Delay`
```shell
root# stap -DMAXSKIPPED=9999 -m ik -g inject_ka.stp 8 17 400
ik module begin:)
```

3. run `dd` to benchmark
```shell
dd if=/dev/zero of=test.dat  bs=8k count=1000000
1000000+0 records in
1000000+0 records out
8192000000 bytes (8.2 GB) copied, 34.8372 s, 235 MB/s
```
4. open inject, start simulate io latency
```shell
root# echo on| tee /proc/systemtap/ik/inject  && sleep 30 && echo off| tee /proc/systemtap/ik/inject
```

5. run `dd` to benchmark again
```shell
dd if=/dev/zero of=test.dat  bs=8k count=1000000
1000000+0 records in
1000000+0 records out
8192000000 bytes (8.2 GB) copied, 79.5475 s, 103 MB/s
```
### Tcp packet loss
We use [tc](https://netbeez.net/blog/how-to-use-the-linux-traffic-control/) to simulate tcp packet loss and latency
#### simulate tcp latency
1. ping www.baidu.com
```shell
root@myali1:~# ping www.baidu.com
PING www.a.shifen.com (220.181.38.149) 56(84) bytes of data.
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=1 ttl=51 time=14.6 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=2 ttl=51 time=14.6 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=3 ttl=51 time=14.6 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=4 ttl=51 time=14.7 ms
^C
--- www.a.shifen.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 14.636/14.664/14.715/0.124 ms
```

2. delay of 100ms
```shell
root@myali1:~# tc qdisc add dev eth0 root netem delay 100ms
```

3. ping www.baidu.com again
```shell
root@myali1:~# ping www.baidu.com
PING www.a.shifen.com (220.181.38.149) 56(84) bytes of data.
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=1 ttl=51 time=114 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=2 ttl=51 time=114 ms
^C
--- www.a.shifen.com ping statistics ---
3 packets transmitted, 2 received, 33% packet loss, time 2003ms
rtt min/avg/max/mdev = 114.761/114.819/114.878/0.343 ms
```

#### simulate tcp packet loss
1. ping www.baidu.com
```shell
root@myali1:~# ping www.baidu.com
PING www.a.shifen.com (220.181.38.149) 56(84) bytes of data.
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=1 ttl=51 time=14.6 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=2 ttl=51 time=14.6 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=3 ttl=51 time=14.6 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=4 ttl=51 time=14.7 ms
^C
--- www.a.shifen.com ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3003ms
rtt min/avg/max/mdev = 14.636/14.664/14.715/0.124 ms
```
2. 30% loss
```shell
root@myali1:~# tc qdisc add dev eth0 root netem loss 30%
```

3. ping www.baidu.com again
```shell
root@myali1:~# ping www.baidu.com
PING www.a.shifen.com (220.181.38.149) 56(84) bytes of data.
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=1 ttl=51 time=14.7 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=4 ttl=51 time=14.8 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=5 ttl=51 time=14.7 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=6 ttl=51 time=14.7 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=7 ttl=51 time=14.7 ms
64 bytes from 220.181.38.149 (220.181.38.149): icmp_seq=8 ttl=51 time=14.7 ms
^C
--- www.a.shifen.com ping statistics ---
8 packets transmitted, 6 received, 25% packet loss, time 7035ms
rtt min/avg/max/mdev = 14.726/14.767/14.886/0.133 ms
```