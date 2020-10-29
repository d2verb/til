# GDB で C++ の名前修飾を demange する
`~/.gdbinit` に以下を追加すればいい。
```
set print asm-demangle on
```

----
参考文献

[1] https://ftp.gnu.org/old-gnu/Manuals/gdb/html_node/gdb_57.html
