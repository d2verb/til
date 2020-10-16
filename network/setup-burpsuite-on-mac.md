# macOS 上で Burp Suite を動かして HTTP/HTTPS 通信を覗きたい
やり方を毎回忘れるのでメモ。 細かいところは省いてる。

## 手順
1. Burp Suite をダウンロードし、インストールする (https://portswigger.net/burp/communitydownload)
2. Burp Suite を立ち上げて、 `[Proxy] > [Options]` タブを開く
3. `Proxy listeners` で Burp Suite の HTTP プロキシサーバが動作しているポートを確認する（必要ならポート番号を `Edit` で変更できる）
4. macOS の `ネットワーク` 設定で、今接続済みのインターフェースの `詳細` をクリックする
5. `プロキシ` タブを開いて `Webプロキシ (HTTP)` と `保護されたWebプロキシ (HTTPS)` に 3. で確認したポート番号を入力し、チェックを付けて `OK` ボタンを押す
6. Burp Suite の `[Proxy] > [Intercept]` タブを開いて、 `Intercept is off` ボタンをクリックして `Intercept is on` 状態にする。
7. Chrome でテキトーなサイトにアクセスして、Burp Suite 側でその通信を捕捉できるか確認する

## トラブルシューティング

### ブラウザで「この接続ではプライバシーが保護されません」というエラーが表示される

Burp Suite の SSL 証明書を登録していないことが原因。
Burp Suite が立ててる HTTP プロキシーサーバにブラウザからアクセスして、証明書をダウンロードし、 `キーチェーンアクセス` で証明証をキーチェーンの追加すればいい。

---
参考文献

[1] https://qiita.com/tomoyk/items/dd9068a69b87f8c7a603
