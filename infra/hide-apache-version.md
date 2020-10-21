# Apache のバージョンを隠す

Apache の設定によっては `Server: Apache/2.0.41 (Unix)` のように、 Apache のバージョンがわかるようなレスポンスヘッダを付けることがある。
しかし、これでは（少なくても私の考えでは）セキュリティ的によろしくないので、バージョン情報は隠したい。バージョン情報を隠すには conf ファイルに以下を書けばいい。

```
ServerTokens Prod (もしくは ServerTokens Prod[uctOnly)
```

また、 `Not Found` ページのフッダに Apache のバージョンが書かれていることがある。これは以下の設定で隠すことができる。

```
ServerSignature Off
```

---
参考文献

[1] https://httpd.apache.org/docs/2.4/ja/mod/core.html#ServerTokens

[2] https://httpd.apache.org/docs/2.4/ja/mod/core.html#ServerSignature
