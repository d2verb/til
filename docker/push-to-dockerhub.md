# ローカルにある Docker イメージを DockerHub に push する

例えば、 DockerHub に d2verb/ubuntuctf というリポジトリがあって、ここにローカルのイメージ ubuntuctf を push したい場合を想定する。

0. 動作中のコンテナからイメージを作成する (必要なら)
```
$ docker commit <RUNNING_CONTAINER_NAME> ubuntuctf:18.04
```

1. リポジトリ名を合わせる。
```
$ docker images
REPOSITORY                                     TAG                            IMAGE ID            CREATED             SIZE
ubuntuctf                                      18.04                          e3c0a1598258        50 minutes ago      1.65GB
...

$ docker tag e3c0a1598258 d2verb/ubuntuctf:18.04

$ docker images
REPOSITORY                                     TAG                            IMAGE ID            CREATED             SIZE
d2verb/ubuntuctf                               18.04                          e3c0a1598258        50 minutes ago      1.65GB
ubuntuctf                                      18.04                          e3c0a1598258        50 minutes ago      1.65GB
```

2. DockerHub に push （事前に `docker login` が必要）
```
$ docker push d2verb/ubuntuctf:18.04
The push refers to repository [docker.io/d2verb/ubuntuctf]
498e9daf7394: Pushed
...
```
