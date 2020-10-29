# copy と deepcopy について

```python
from copy import copy, deepcopy

data = {
    "class": "6-A",
    "students": [
        {
            "name": "alice",
            "age": 12
        },
        {
            "name": "bob",
            "age": 12
        }
    ]
}

copied = copy(data)
print(id(copied) != id(data), id(copied["students"]) != id(data["students"]))

deepcopied = deepcopy(data)
print(id(deepcopied) != id(data), id(deepcopied["students"]) != id(data["students"]))
```

```
$ python copy_deepcopy.py
True False
True True
```

copy も deepcopy もコピー後のオブジェクト id がコピー前のものと異なっている。しかし、以下の点でことなる。
- **copy**: students の value のオブジェクト id が同じ。
- **deepcopy**: students の value のオブジェクト id が異なる。

つまり、 deepcopy は引数で渡したオブジェクトのオブジェクト中で見つかった別のオブジェクトのコピーも作るが、copy は、引数で渡したオブジェクトのコピーのみを作る。

ドキュメントによると、正確には以下のような違いがある。
> 浅いコピー (shallow copy) は新たな複合オブジェクトを作成し、その後 (可能な限り) 元のオブジェクト中に見つかったオブジェクトに対する 参照 を挿入します。
>
> 深いコピー (deep copy) は新たな複合オブジェクトを作成し、その後元のオブジェクト中に見つかったオブジェクトの コピー を挿入します。

----
参考文献

[1] https://docs.python.org/ja/3/library/copy.html
