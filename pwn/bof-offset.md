# Buffer Overflow 発生時に buffer 先頭からリターンアドレスまでの offset を調べる
今まで、 bof を発見したときは、 buffer の先頭からリターンアドレスが格納されている領域までの offset を手動で計算していたが、もっと簡単な方法があったので、メモがてら記録しておく。
なお、ここでは gdb の exploit helper である gef を使うことを前提とする。

手順としては
1. `pattern create [パターンの長さ]` でパターンを作る
2. 1 で生成したパターンをプログラムに入力する
3. リターンアドレスが上書きされているなら、上書きされた値を使って `pattern search [値]` で offset 検索する

```
root@e0e8cb60dc66:/ctf/login3# gdb -q ./login3
GEF for linux ready, type `gef' to start, `gef config' to configure
80 commands loaded for GDB 8.1.0.20180409-git using Python engine 3.6
Reading symbols from ./login3...(no debugging symbols found)...done.
gef➤  pattern create 128
[+] Generating a pattern of 128 bytes
aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaa
[+] Saved as '$_gef0'
gef➤  run
Starting program: /ctf/login3/login3
warning: Error disabling address space randomization: Operation not permitted
ID: aaaaaaaabaaaaaaacaaaaaaadaaaaaaaeaaaaaaafaaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaa
Invalid ID

Program received signal SIGSEGV, Segmentation fault.
[ Legend: Modified register | Code | Heap | Stack | String ]
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── registers ────
$rax   : 0x0
$rbx   : 0x0
$rcx   : 0x00007f9296b04264  →  0x5477fffff0003d48 ("H="?)
$rdx   : 0x00007f9296de18c0  →  0x0000000000000000
$rsp   : 0x00007ffdb0d2ab08  →  "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"
$rbp   : 0x6161616161616165 ("eaaaaaaa"?)
$rsi   : 0x00007f9296de07e3  →  0xde18c0000000000a
$rdi   : 0x1
$rip   : 0x0000000000401267  →  <main+134> ret
$r8    : 0xa
$r9    : 0x00007f92970004c0  →  0x00007f92970004c0  →  [loop detected]
$r10   : 0x3
$r11   : 0x246
$r12   : 0x0000000000401090  →  <_start+0> endbr64
$r13   : 0x00007ffdb0d2abe0  →  0x0000000000000001
$r14   : 0x0
$r15   : 0x0
$eflags: [zero carry PARITY adjust sign trap INTERRUPT direction overflow RESUME virtualx86 identification]
$cs: 0x0033 $ss: 0x002b $ds: 0x0000 $es: 0x0000 $fs: 0x0000 $gs: 0x0000
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── stack ────
0x00007ffdb0d2ab08│+0x0000: "faaaaaaagaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaala[...]"	 ← $rsp
0x00007ffdb0d2ab10│+0x0008: "gaaaaaaahaaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaama[...]"
0x00007ffdb0d2ab18│+0x0010: "haaaaaaaiaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaana[...]"
0x00007ffdb0d2ab20│+0x0018: "iaaaaaaajaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoa[...]"
0x00007ffdb0d2ab28│+0x0020: "jaaaaaaakaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapa[...]"
0x00007ffdb0d2ab30│+0x0028: "kaaaaaaalaaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaa"
0x00007ffdb0d2ab38│+0x0030: "laaaaaaamaaaaaaanaaaaaaaoaaaaaaapaaaaaaa"
0x00007ffdb0d2ab40│+0x0038: "maaaaaaanaaaaaaaoaaaaaaapaaaaaaa"
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── code:x86:64 ────
     0x40125c <main+123>       call   0x401030 <puts@plt>
     0x401261 <main+128>       mov    eax, 0x0
     0x401266 <main+133>       leave
 →   0x401267 <main+134>       ret
[!] Cannot disassemble from $PC
──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── threads ────
[#0] Id 1, Name: "login3", stopped 0x401267 in main (), reason: SIGSEGV
────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────── trace ────
[#0] 0x401267 → main()
─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
0x0000000000401267 in main ()
gef➤  pattern search $rsp
[+] Searching '$rsp'
[+] Found at offset 40 (little-endian search) likely
[+] Found at offset 33 (big-endian search)
gef➤
```

---
参考文献

[1] https://gef.readthedocs.io/en/master/commands/pattern/
