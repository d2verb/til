# Fetch API でレスポンスのステータスコードが 404 の場合に reject したい
Fetch API はステータスコード 404 のときは promise を reject しない。 404 のときも reject したい場合は以下のようにすればいい。
```javascript
fetch("http://example.com/api/articles")
  .then(response => {
    if (response.ok) {
      return response.json();
    }
    return Promise.reject();
  })
  .then(response => {
    // solved
  })
  .catch(error => {
    // rejected
  });
```
ちなみに、 `response.ok` はステータスコードが 200 ~ 299 の範囲内にある場合に true になる。したがって、正確にいうと上記のコードはステータスコードが 200 ~ 299 の範囲外の場合に promise を reject する。

-----
参考文献

[1] https://developer.mozilla.org/ja/docs/Web/API/Fetch_API

[2] https://developer.mozilla.org/ja/docs/Web/API/Response
