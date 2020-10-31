# macOS で実行ファイルがリンクしている共有ライブラリを調べる
Linux だったら ldd コマンドで調べられる。 macOS では otool を使う。
```bash
$ otool -L /usr/bin/php
/usr/bin/php:
	/usr/lib/libresolv.9.dylib (compatibility version 1.0.0, current version 1.0.0)
	/usr/lib/libcrypto.44.dylib (compatibility version 45.0.0, current version 45.1.0)
	/usr/lib/libssl.46.dylib (compatibility version 47.0.0, current version 47.1.0)
	/usr/lib/libexslt.0.dylib (compatibility version 9.0.0, current version 9.15.0)
	/usr/lib/libtidy.A.dylib (compatibility version 1.0.0, current version 1.0.0)
	/usr/lib/libedit.3.dylib (compatibility version 2.0.0, current version 3.0.0)
	/usr/lib/libncurses.5.4.dylib (compatibility version 5.4.0, current version 5.4.0)
	/usr/lib/libpq.5.dylib (compatibility version 5.0.0, current version 5.6.0)
	/System/Library/Frameworks/LDAP.framework/Versions/A/LDAP (compatibility version 1.0.0, current version 2.4.0)
	/usr/lib/libiconv.2.dylib (compatibility version 7.0.0, current version 7.0.0)
	/usr/lib/libz.1.dylib (compatibility version 1.0.0, current version 1.2.11)
	/usr/lib/libbz2.1.0.dylib (compatibility version 1.0.0, current version 1.0.5)
	/usr/lib/libSystem.B.dylib (compatibility version 1.0.0, current version 1281.100.1)
	/usr/lib/libxml2.2.dylib (compatibility version 10.0.0, current version 10.9.0)
	/usr/lib/libicucore.A.dylib (compatibility version 1.0.0, current version 64.2.0)
	/System/Library/Frameworks/Kerberos.framework/Versions/A/Kerberos (compatibility version 5.0.0, current version 6.0.0)
	/usr/lib/libcurl.4.dylib (compatibility version 7.0.0, current version 9.0.0)
	/usr/lib/libnetsnmp.25.dylib (compatibility version 26.0.0, current version 26.2.0)
	/usr/lib/libxslt.1.dylib (compatibility version 3.0.0, current version 3.26.0)
```
