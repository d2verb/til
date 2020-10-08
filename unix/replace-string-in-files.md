# 複数のファイル内の文字列を置換したい

置換前の文字列を `hoge` 、置換後の文字列を `fuga` とすると

```
$ grep -l "hoge" ./* | xargs sed -i -e "s/hoge/fuga/g"
```
