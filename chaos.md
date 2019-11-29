## Why chaos engineering useful
Chaos engineering can be used to achieve resilience against:
* Infrastructure failures
* Network failures
* Application failures

Alibaba has a toolkit [Chaosblade](https://github.com/chaosblade-io/chaosblade) easy to use and powerful chaos engineering experiment 

### Disk io latency
We use `systemtap` to simulate io latency
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
We use `tc` to simulate io latency