# Laravel で Notifiable なしで通知を送る
Laravel の通知は、特定の通知方法 (Channel) を通じて、通知メッセージ (Notification) を通知先 (Notifiable) に届ける。
基本的に、これらの ３ 要素のそれぞれにおいて、クラスを用意する必要があるが、場合によっては、 Notifiable を作る必要がないと思う場面に出くわす（slack webhook や sns topic にメッセージを送るときなど）。
このような場面では、 AnonymousNotifiable が役に立つ。

本記事では、明示的に Notifiable クラスを作らずに AnonymousNotifiable を利用し、 Channel と Notification のみを使って通知を実装する方法を紹介する。

なお、検証に使用した Laravel のバージョンは 7 である。

## 実現方法
**Notification**
```php
<?php

namespace App\Notifications;

use App\Channels\MessageChannel;
use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Notification;

class Message extends Notification
{
    use Queueable;

    /**
     * Create a new notification instance.
     *
     * @return void
     */
    public function __construct(string $message)
    {
        $this->message = $message;
    }

    /**
     * Get the notification's delivery channels.
     *
     * @return array
     */
    public function via()
    {
        return [MessageChannel::class];
    }

    /**
     * Get the message representation of the notification.
     *
     * @param  mixed  $notifiable
     * @return string
     */
    public function toMessageChannel()
    {
        return $this->message;
    }
}
```

**Message Channel**
```php
<?php

namespace App\Channels;

use Illuminate\Support\Facades\Log;

class MessageChannel
{
    public function send($notifiable, $notification)
    {
        $message = $notification->toMessageChannel();
        $route = $notifiable->routeNotificationFor(self::class);
        Log::info([$message, $route]);
    }
}
```

上記の 2 つのファイルを用意して、 tinker で以下の一連のコードを実行する。
```
$ php artisan tinker
Psy Shell v0.10.4 (PHP 7.4.5 — cli) by Justin Hileman
>>> use Illuminate\Support\Facades\Notification;
>>> use App\Channels\MessageChannel;
>>> use App\Notifications\Message;
>>> Notification::route(MessageChannel::class, "route info")->notify(new Message("hello!"));
=> null
```

すると `storage/logs/laravel.log` に以下のようなログが出力されるはずである。これを確認できれば成功。
```
[2020-10-15 14:55:45] local.INFO: array (
  0 => 'hello!',
  1 => 'route info',
)  
```

## テスト方法

```php
<?php

namespace Tests\Feature;

use App\Notifications\Message;
use Illuminate\Notifications\AnonymousNotifiable;
use Illuminate\Support\Facades\Notification;
use Tests\TestCase;

class NotificationTest extends TestCase
{
    public function testNotification()
    {
        Notification::fake();

        Notification::route(MessageChannel::class, "route info")->notify(new Message("hello!"));

        Notification::assertSentTo(
            new AnonymousNotifiable(),
            Message::class
        );
    }
}
```
