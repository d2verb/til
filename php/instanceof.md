# PHP の instanceof は親クラスもインターフェースもチェックできる
もはや表題の通りだが、一応サンプルコードを以下に示しておく。
```php
<?php

interface NullInterface {}
class Base {}
class Derived extends Base implements NullInterface {}

$d = new Derived();

var_dump($d instanceof Base);
var_dump($d instanceof Derived);
var_dump($d instanceof NullInterface);
```

実行結果
```php
$ php instanceof.php                                                                                                                                       ✘
bool(true)
bool(true)
bool(true)
```

-----
参考文献

[1] https://www.php.net/manual/ja/language.operators.type.php
