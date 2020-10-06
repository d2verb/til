# Laravel8 でも afterCreatingState したい

ステートメソッドの中に `afterCreating` を書くといい。

```php
public function public()
{
  return $this->state([
    'is_public' => 1
  ])->afterCreating(function ($post) {
    // ここに afterCreatingState の中身を書く
  });
}
```
