# Laravel の validated の存在意義
Laravel の FormRequest には validated() なるメソッドがあり、これの存在意義があまりわかっていなかったが、今日なんとなくわかったのでメモ。

validated() の API document [1] には以下のような説明が書かれている。
> Get the validated data from the request.

バリデート済みのデータなら validated() なしでも取れるのだが…と思っていたが、以下のサンプルでなるほどと思った。

```php
<?php

namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class SampleRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }

    public function rules()
    {
        return [
            'name' => 'required|string',
        ];
    }
}
```

```php
<?php

namespace App\Http\Controllers;

use App\Http\Requests\SampleRequest;

class HomeController extends Controller
{
    public function index(SampleRequest $request)
    {
        return response()->json([
            'all' => $request->all(),
            'validated' => $request->validated()
        ]);
    }
}
```

このコードを以下のコマンドで動かしてみると以下のような出力が表示される。

```
$ http -b http://localhost:8080/\?name\=hoge\&age\=20
{
    "all": {
        "age": "20",
        "name": "hoge"
    },
    "validated": {
        "name": "hoge"
    }
}
```

出力から明らかだが、validated() はバリデーションルールに書かれていないクエリパラメータを除外している。
`$request->name` のように、パラメータを陽に指定している場合は validated() は必要がない。しかし、バリデート済みのすべてのパラメータに対して、同一の処理をしたい場面では validated() は活きるのではないだろうか。

---
参考文献

[1] https://laravel.com/api/8.x/Illuminate/Foundation/Http/FormRequest.html#method_validated
