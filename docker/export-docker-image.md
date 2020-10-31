# Docker コンテナイメージの中身を取り出す

イメージ `ubuntu:20.04` の中身を取り出したいとする。

```bash
$ docker run --name ubuntu2004 -d ubuntu:20.04
$ docker export --output="ubuntu2004.tar" ubuntu2004
$ ls
ubuntu2004.tar
$ mkdir ubuntu2004 && mv ubuntu2004.tar ubuntu2004 && cd ubuntu2004 && tar xvf ubuntu2004.tar
$ ls
boot  dev  etc  home  media  mnt  opt  proc  root  run  srv  sys  tmp  usr  var  bin  lib  lib32  lib64  libx32  sbin  ubuntu2004.tar
```

---
参考文献

[1] https://docs.docker.com/engine/reference/commandline/export/
