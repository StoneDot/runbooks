# Linux

## コマンドの紹介
以下では Linux で共通して使用できる、一般的なコマンドについて記載します。

### プロセスの確認
すべてのプロセスについての確認は、以下のコマンドが利用できます。

```bash
# UNIX style
$ ps -eFl | head -3
F S UID        PID  PPID  C PRI  NI ADDR SZ WCHAN    RSS PSR STIME TTY          TIME CMD
4 S root         1     0  0  80   0 - 29351 -       5272   1 15:24 ?        00:00:04 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
1 S root         2     0  0  80   0 -     0 -          0   1 15:24 ?        00:00:00 [kthreadd]

# Thread の情報も含める
$ ps -eFlL | head -3
F S UID        PID  PPID   LWP  C NLWP PRI  NI ADDR SZ WCHAN    RSS PSR STIME TTY          TIME CMD
4 S root         1     0     1  0    1  80   0 - 29351 -       5292   0 15:24 ?        00:00:04 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
1 S root         2     0     2  0    1  80   0 -     0 -          0   1 15:24 ?        00:00:00 [kthreadd]

# Security 関係の情報。詳細については
$ ps -eo euser,ruser,suser,fuser,f,comm,label | head -3
EUSER    RUSER    SUSER    FUSER    F COMMAND         LABEL
root     root     root     root     4 systemd         -
root     root     root     root     1 kthreadd        -
```

特定のプロセスについての確認は、以下のコマンドが使用できます。

```bash
# プロセスに関係する一般の情報
$ ps -Fl -1
F S UID        PID  PPID  C PRI  NI ADDR SZ WCHAN    RSS PSR STIME TTY        TIME CMD
4 S root         1     0  0  80   0 - 29351 -       5272   0 15:24 ?          0:04 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
# プロセスに適用されている制限
$ cat /proc/1/limits | head -3
Limit                     Soft Limit           Hard Limit           Units
Max cpu time              unlimited            unlimited            seconds
Max file size             unlimited            unlimited            bytes
```