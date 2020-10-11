# Python のバイトコードが見たい
まずは、バイトコードが見たいファイルをコンパイルする。
```
$ cat sample.py
def hello(name):
    print(name)

hello("Tom")
$ python -m compileall sample.py
```

そうすると、カレントディレクトリに `__pycache__` ディレクトリができて、この中にコンパイル済みのバイトコードが格納されている。
```
$ ls __pycache__
sample.cpython-38.pyc
```

この `.pyc` を逆アセンブルする方法は 2 通りある。1 つは `dis.dis()` を使う方法。もう 1 つは `dis.Bytecode` の `dis()` を使う方法。

## `dis.dis()` を使う方法
```python
import dis
import marshal
import sys

if len(sys.argv) < 2:
    sys.exit(0)

with open(sys.argv[1], "rb") as f:
    f.seek(16)
    m = marshal.load(f)
    print(dis.dis(m))
```
これを実行すると
```
$ python disas.py ./__pycache__/sample.cpython-38.pyc
  1           0 LOAD_CONST               0 (<code object hello at 0x105778ea0, file "sample.py", line 1>)
              2 LOAD_CONST               1 ('hello')
              4 MAKE_FUNCTION            0
              6 STORE_NAME               0 (hello)

  4           8 LOAD_NAME                0 (hello)
             10 LOAD_CONST               2 ('Tom')
             12 CALL_FUNCTION            1
             14 POP_TOP
             16 LOAD_CONST               3 (None)
             18 RETURN_VALUE

Disassembly of <code object hello at 0x105778ea0, file "sample.py", line 1>:
  2           0 LOAD_GLOBAL              0 (print)
              2 LOAD_FAST                0 (name)
              4 CALL_FUNCTION            1
              6 POP_TOP
              8 LOAD_CONST               0 (None)
             10 RETURN_VALUE
None
```

## `dis.Bytecode` の `dis()` を使う方法
```python
import dis
import marshal
import sys

if len(sys.argv) < 2:
    sys.exit(0)

dumped = []

def dump(codeobj):
    if codeobj in dumped:
        return

    dumped.append(codeobj)

    bc = dis.Bytecode(codeobj)
    print(bc.dis())

    for const in codeobj.co_consts:
        if not hasattr(const, "co_consts"):
            continue
        dump(codeobj)

with open(sys.argv[1], "rb") as f:
    f.seek(16)
    m = marshal.load(f)
    print(dump(m))
```
これを実行すると
```
$ python disas.py ./__pycache__/sample.cpython-38.pyc
  1           0 LOAD_CONST               0 (<code object hello at 0x10b5b79d0, file "sample.py", line 1>)
              2 LOAD_CONST               1 ('hello')
              4 MAKE_FUNCTION            0
              6 STORE_NAME               0 (hello)

  4           8 LOAD_NAME                0 (hello)
             10 LOAD_CONST               2 ('Tom')
             12 CALL_FUNCTION            1
             14 POP_TOP
             16 LOAD_CONST               3 (None)
             18 RETURN_VALUE

None
```

一見すると、 `Bytecode` を使うほうが面倒くさそうだが、`Bytecode` には `info()` のような便利なメソッドがあったりするので、場合によっては使っていきたい。
```
$ python
Python 3.8.2 (default, Apr 14 2020, 13:29:18)
[Clang 11.0.3 (clang-1103.0.32.29)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> import dis
>>> import marshal
>>> f = open("__pycache__/sample.cpython-38.pyc", "rb")
>>> f.seek(16)
16
>>> m = marshal.load(f)
>>> bc = dis.Bytecode(m)
>>> print(bc.info())
Name:              <module>
Filename:          sample.py
Argument count:    0
Positional-only arguments: 0
Kw-only arguments: 0
Number of locals:  0
Stack size:        2
Flags:             NOFREE
Constants:
   0: <code object hello at 0x10f379b30, file "sample.py", line 1>
   1: 'hello'
   2: 'Tom'
   3: None
Names:
   0: hello
```
