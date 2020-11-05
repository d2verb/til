# Linux の cgroup のメモ
cgroup (control group) はプロセスをグループ化し、グループに対して CPU やメモリなどのリソースを制限できる Linux の機能である。ここでは、 cgroup の使い方をすこし説明する。

## cgroupfs

cgroup は cgroupfs というファイルシステムを介して利用できる。
```bash
$ pwd
/sys/fs/cgruop
$ ls
blkio  cpu,cpuacct  cpuset  devices  freezer  hugetlb  memory  net_cls,net_prio  perf_event  pids  rdma  systemd  unified  cpu  cpuacct  net_cls  net_prio
$ ls cpu
system.slice  user.slice             cgroup.procs          cpu.cfs_period_us  cpu.shares  cpuacct.stat   cpuacct.usage_all     cpuacct.usage_percpu_sys   cpuacct.usage_sys   notify_on_release  tasks
test          cgroup.clone_children  cgroup.sane_behavior  cpu.cfs_quota_us   cpu.stat    cpuacct.usage  cpuacct.usage_percpu  cpuacct.usage_percpu_user  cpuacct.usage_user  release_agent
```

特定のリソースに対するグループを作るには、単にそのリソースのディレクトリ内に新しいディレクトリを切ればいい。例えば、CPU リソースに対して test というグループを作りたい場合は
```bash
$ pwd
/sys/fs/cgroup/cpu
$ sudo mkdir test
$ ls test
cgroup.clone_children  cpu.cfs_period_us  cpu.shares  cpu.uclamp.max  cpuacct.stat   cpuacct.usage_all     cpuacct.usage_percpu_sys   cpuacct.usage_sys   notify_on_release
cgroup.procs           cpu.cfs_quota_us   cpu.stat    cpu.uclamp.min  cpuacct.usage  cpuacct.usage_percpu  cpuacct.usage_percpu_user  cpuacct.usage_user  tasks
```

グループにプロセスを登録するには、グループディレクトリ内の task というファイルに pid を書き込めばいい。例えば、現在のシェルを test グループに登録するには
```bash
$ pwd
/sys/fs/cgroup/cpu/test
$ echo $$
6231
$ echo $$ | sudo tee ./tasks
$ cat ./tasks
6231
7721
```

ここで、 7721 は cat コマンドの pid である。登録したシェルから fork した新しいプロセス（ここでは cat コマンド）も同じグループに属するのである。

## リソース制限
ここでは、例として CPU とメモリを cgroup で制限する方法をまとめる。
例えば、 `cpu.cfs_quota_us` に 30 ms、 `cpu.cfs_period_us` に 100ms を設定すれば、 CPU 使用率を 30% にできる。各パラメータは以下のような意味がある。
- `cpu.cfs_period_us`: グループ内のプロセスが次に CPU リソースにアクセスできるまでの時間
- `cpu.cfs_quota_us`: 一定の期間にプロセスが実行される合計時間

イメージとしては、以下のような感じ。
```
                   period_us (100 ms)
 /-------------------------^-------------------------\
|#####################|-------------------------------|#####################|--------> (CPU 時間)
 \---------v---------/
    quota_us (30ms)
    
#: プロセスが実行できる時間
```

設定方法は
```bash
$ pwd
/sys/fs/cgroup/cpu/test
$ echo 30000 | sudo tee cpu.cfs_quota_us
$ echo 100000 | sudo tee cpu.cfs_period_us
$ cat cpu.cfs_quota_us
30000
$ cat cpu.cfs_period_us
100000
```

top コマンドで確認してみると
```bash
$ top -p 6231
  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND
 6231 username  20   0   63628   8844   4488 R  30.0  0.1   8:32.61 zsh
```

---
参考文献

[1] https://gihyo.jp/admin/serial/01/linux_containers/0004?page=2

[2] https://access.redhat.com/documentation/ja-jp/red_hat_enterprise_linux/6/html/resource_management_guide/sec-cpu
