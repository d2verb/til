# serverless framework で Python のパッケージをインストールしてデプロイしたい
デフォルトでは pip でインストールしたパッケージが反映されなかったので対処策を調べた。結論をいうと serverless-python-requirements という serverless framework のプラグインをインストールすればいい。

```
$ sls plugin install -n serverless-python-requirements
```

上記コマンドを実行した後、 `serverless.yml` に以下を追加する。

```yaml
plugins:
  - serverless-python-requirements
```

また、 non-pure-Python なパッケージをビルドしたい場合 `serverless.yml` に以下を追加しておく。 `docker-lmabda` イメージの環境内でビルドが実行される。
なお、`dockerizePip: non-linux` とすると non-linux の環境でのみ `dockerizePip` が効くようになる。

```
custom:
  pythonRequirements:
    dockerizePip: true
```

---
参考文献

[1] https://www.serverless.com/plugins/serverless-python-requirements
