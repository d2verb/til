# macOS で実行ファイルがリンクしている共有ライブラリを調べる
Linux だったら ldd コマンドで調べられる。 macOS では otool を使う。
```bash
$ otool -L ./a.out
./a.out:
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1281.100.1)
```
