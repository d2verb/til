# unittest で標準出力をモックする

```python
from unittest import TestCase, main
from unittest.mock import patch
from io import StringIO

def greet(name):
    print(f"Hello, {name}!")

class StdoutTest(TestCase):
    def test_sample(self):
        with patch("sys.stdout", new=StringIO()) as stdout_mock:
            greet("Alice")
            greet("Bob")
            self.assertEqual(stdout_mock.getvalue(), "Hello, Alice!\nHello, Bob!\n")

if __name__ == "__main__":
    main()
```

```bash
$ python test.py
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```
