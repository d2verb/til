# グローバルな .gitignore を作る

```
$ touch ~/.gitignore
$ echo .envrc > ~/.gitignore
$ git config --global core.excludesFile ~/.gitignore
```

~/.gitconfig の中身を見てみると以下が書き込まれていることがわかる。

```
[core]
	excludesfile = /Users/<YOURNAME>/.gitignore
```

---
参考
- https://qiita.com/takuya0301/items/29187aa088d04cf92659
