# Laravel でパスワードに使える文字に制限を課すバリデーションを作る
例:
- 文字数が12文字以上、50文字以下
- 英大文字、英小文字、数字のみで構成される
- 英大文字、英小文字、数字すべてが使われている

```php

class PasswordUpdateRequest extends FormRequest
{
  public function rules()
    {
        return [
            'password' => [
                'required',
                'max:50',
                'min:12',
                'regex:/\A(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[a-zA-Z0-9]+\z/',
            ],
        ];
    }
}
```
