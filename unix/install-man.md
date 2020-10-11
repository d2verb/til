# man コマンドをインストールしたい

基本的には以下のコマンドで事足りる。
```
$ apt install -y man man-db manpages-posix-dev manpages-posix
```

しかし、 Linux の Docker イメージでは、イメージサイズの削減のためか man や documentations 類をインストールしないよう設定されていることがある。
```
$ cat /etc/dpkg/dpkg.cfg.d/excludes
# Drop all man pages
path-exclude=/usr/share/man/*

# Drop all translations
path-exclude=/usr/share/locale/*/LC_MESSAGES/*.mo

# Drop all documentation ...
path-exclude=/usr/share/doc/*

# ... except copyright files ...
path-include=/usr/share/doc/*/copyright

# ... and Debian changelogs
```

このファイルを削除するなり、該当行をコメントアウトするなりして、本記事先頭のコマンドを実行すればいい。もし実行済みの場合は以下のように再インストールすればいい。
```
apt install -y --reinstall man man-db manpages-posix-dev manpages-posix
```

---
参考文献

[1] https://github.com/tianon/docker-brew-ubuntu-core/issues/122#issuecomment-380517413
