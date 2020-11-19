# Rust でライブラリのエラーもしくは None を独自エラーにラッピング

## 1. ライブラリエラー to 独自エラー
```rust
File::create(&filename).map_err(
  |source| OriginalError::new()
)
```

## 2. None to 独自エラー
```rust
string.pop().ok_or(
  OriginalError::new()
)
```

---
参考文献

[1] https://doc.rust-lang.org/stable/std/result/enum.Result.html#method.map_err

[2] https://doc.rust-lang.org/std/option/enum.Option.html#method.ok_or
