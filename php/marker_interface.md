# マーカーインターフェース
メソッドやフィールドが一切定義されていないインターフェース。実行時のオブジェクトに関する「ヒント」を得るために使われる。知らず知らずのうちに使っていたが、ちゃんと名前があるらしい。

## 例
エラーを通知しないクラスを表現するための `NeverNotify` クラス。

```php
// NeverNotify.php
interface NeverNotify
{
}

// LicenseLimitExceededException.php
class LicenseLimitExceededException extends BaseApiException implements NeverNotify
{
}

// Handler.php
class Handler extends ExceptionHandler
{
  public function report(Throwable $exception)
  {
    if (!($exception instanceof NeverNotify)) {
      Notification::route(SnsTopicChannel::class, config('const.sns_topic_arn'))->notify(new Alert($exception));
    }
  }
}
```

---
参考文献

[1] https://ja.wikipedia.org/wiki/%E3%83%9E%E3%83%BC%E3%82%AB%E3%83%BC%E3%82%A4%E3%83%B3%E3%82%BF%E3%83%95%E3%82%A7%E3%83%BC%E3%82%B9

[2] https://www2.slideshare.net/devworks/laravel-php2020-239980059/devworks/laravel-php2020-239980059
