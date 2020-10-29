# Laravel でテストで app コンテナが作られるタイミングと注意すべきこと
Laravel のテストにおいて、app コンテナが作られるのは `Illuminate\Foundation\Testing\TestCase` の `setUp()` 内である。
より正確には `setUp() -> refreshApplication() -> createApplication()` の順に処理が実行され、`createApplication()` 内で app コンテナが作られる。

## app コンテナがないとできないこと
app コンテナが存在しない場合では、例えば以下のことができない。
- ファザードを使う
- ヘルパ関数を使う
- マイグレーションを実行したり、シーダーを流す

## テスト内で app コンテナが存在しないケース
テストの種類や実行タイミングによっては app コンテナが存在しないので、上記の処理に依存する処理は書けない。
app コンテナが存在しないタイミングの例として以下がある。
1. data provider 内 (`setUp()` よりも前に実行されるため)
2. `setUpBeforeClass()` 内 (`setUp()` よりも前に実行されるため)
3. unit test 内 (`Illuminate\Foundation\Testing\TestCase` を継承しないため)
