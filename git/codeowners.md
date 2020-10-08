# GitHub のコードオーナー
GitHub では、CODEOWNERS ファイルを使い、リポジトリ内のコードに対して誰が責任を負うのかを指定できる。他者が所有するコードを変更するプルリクをオープンすると、自動的にその人にレビューリクエストを投げる。

CODEOWNERS ファイルは、リポジトリのルート、`docs/`、`.github/`のいずれかのディレクトリに作る。

ファイル内容例
```
# js ファイルを変更すると、@dev-team にレビューリクエストが飛ぶ
*.js @dev-team

# api-definition.yaml を変更すると、@dev-team と @architect の両方にレビューリクエストが飛ぶ
api-definition.yaml @dev-team @architect
```

---
参考文献

[1] https://docs.github.com/ja/free-pro-team@latest/github/creating-cloning-and-archiving-repositories/about-code-owners
