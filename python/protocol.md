# Python で静的ダックタイピング
インターフェースの実装を表現するために用いられる一般的な方法は継承である。
```python
from abc import ABC, abstractmethod

class Responsable(ABC):
    @abstractmethod
    def to_response(self) -> str:
        ...

class JsonResponse(Responsable):
    def to_response(self) -> str:
        return '{"status": "ok"}'

class HTMLResponse(Responsable):
    def to_response(self) -> str:
        return '<span>status: ok</span>'

def dump_response(resp: Responsable) -> None:
    print(resp.to_response())

dump_response(JsonResponse())
dump_response(HTMLResponse())
```
もちろん、型検査は通る。
```
$ python -m mypy nominal.py
Success: no issues found in 1 source file
```
しかし、インターフェースを実装するたびに継承するのは面倒臭いので、継承なしでのインターフェースの実装を実現したい。これに Protocol が使える。
```python
from typing import Protocol

class Responsable(Protocol):
    def to_response(self) -> str:
        ...

class JsonResponse:
    def to_response(self) -> str:
        return '{"status": "ok"}'

class HTMLResponse:
    def to_response(self) -> str:
        return '<span>status: ok</span>'

def dump_response(resp: Responsable) -> None:
    print(resp.to_response())

dump_response(JsonResponse())
dump_response(HTMLResponse())
```
これも型検査が通る。
```
$ python -m mypy structural.py
Success: no issues found in 1 source file
```

詳しくは [PEP544](https://www.python.org/dev/peps/pep-0544/) を参照すべし。
