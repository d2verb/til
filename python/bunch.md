# コンストラクタで任意の属性を指定できるクラス
Bunch パターンというらしい。

```python
class Bunch(dict):
  def __init__(self, *args, **kwds):
    super().__init__(*args, **kwds)
    self.__dict__ = self
```

こんな感じで使える。

```python
>>> Bunch(name="tom", age=21)
{'name': 'tom', 'age': 21}
>>> tom = Bunch(name="tom", age=21)
>>> tom.name
'tom'
>>> tom.age
21
>>> tom["name"]
'tom'
>>> tom["age"]
```

---
参考文献

[1] https://gihyo.jp/book/2020/978-4-297-11686-6, p.45
