# Logging

## rsyslog

Linux で多く使われているログフォワードエージェント rsyslog の設定方法については [ngyuki さんのブログ](https://ngyuki.hatenablog.com/entry/2016/04/18/220724) がわかりやすいです。

`man rsyslog.conf` で設定ファイルの書き方を確認できます。

表は [systemd/ジャーナル](https://wiki.archlinux.jp/index.php/Systemd/%E3%82%B8%E3%83%A3%E3%83%BC%E3%83%8A%E3%83%AB)
の内容をそのままいただきました。

### コマンド

```shell
# 設定の確認
rsyslogd -N1
# デーモンの再起動
sudo systemctl restart rsyslog
```

### 記述例
以下のように「ファシリティ」、「プライオリティレベル」、「アクション」の順番で記述します。

```config
mail.info /var/log/message 
```

以下のように記述することで `td-agent-bit` のログを `/var/log/td-agent-bit.log` に保存できます。
stop を記述しているため、`/var/log/messages` の出力前に記述すると、`/var/log/message` へのログ出力を抑制します。 

```config
:programname, isequal, "td-agent-bit" /var/log/td-agent-bit.log
& stop
```


### プライオリティレベル

| 値 |     重大度    | キーワード |                          説明                          |                                                                                                        例                                                                                                       |
|:--:|:-------------:|:----------:|:------------------------------------------------------:|:---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------:|
|  0 | Emergency     | emerg      | システムは利用不可能                                   | 重大なカーネルのバグ/systemd によってコアダンプが作成された。アプリケーションが使用するレベルではありません。                                                                                                   |
|  1 | Alert         | alert      | 即時対応が必要                                         | 重要なサブシステムが機能していない/データが消失している。 kernel: BUG: unable to handle kernel paging request at ffffc90403238ffc。                                                                             |
|  2 | Critical      | crit       | 緊急状態                                               | クラッシュ/コアダンプ。systemd-coredump[25319]: Process 25310 (plugin-containe) of user 1000 dumped core。X11 などのシステムにとって重要なアプリケーションが機能を失っている。                                  |
|  3 | Error         | err        | エラー状態                                             | 重大度は低いエラー: kernel: usb 1-3: 3:1: cannot get freq at ep 0x84, systemd[1]: Failed unmounting /var., libvirtd[1720]: internal error: Failed to initialize a valid firewall backend)。                     |
|  4 | Warning       | warning    | エラーが発生する前に対応が必要                         | ルートファイルシステム以外のファイルシステムの空き領域が 1GB しかない。org.freedesktop. Notifications[1860]: (process:5999): Gtk-WARNING **: Locale not supported by C library. Using the fallback 'C' locale。 |
|  5 | Notice        | notice     | 通常ではないイベントが発生しているがエラー状態ではない | systemd[1]: var.mount: Directory /var to mount over is not empty, mounting anyway, gcr-prompter[4997]: Gtk: GtkDialog mapped without a transient parent. This is discouraged。                                  |
|  6 | Informational | info       | 何も対応が必要な通常のメッセージ                       | lvm[585]: 7 logical volume(s) in volume group "archvg" now active。                                                                                                                                             |
|  7 | Debug         | debug      | アプリケーションをデバッグするのに役立つ情報           | kdeinit5[1900]: powerdevil: Scheduling inhibition from ":1.14" "firefox" with cookie 13 and reason "screen"。                                                                                                   |


### ファシリティ

|    | キーワード |                          説明                         |                                          情報                                         |
|:--:|:----------:|:-----------------------------------------------------:|:-------------------------------------------------------------------------------------:|
|  0 | kern       | カーネルメッセージ                                    |                                                                                       |
|  1 | user       | ユーザーレベルメッセージ                              |                                                                                       |
|  2 | mail       | メールシステム                                        | 詳しくは mail(1) を見てください。                                                     |
|  3 | daemon     | システムデーモン                                      | systemd やサブシステムを含む全てのデーモン                                            |
|  4 | auth       | セキュリティ/認証メッセージ                           | ファシリティ10もあります。                                                            |
|  5 | syslog     | syslogd の内部メッセージ                              | syslogd 用のファシリティなので systemd は使用していません (ファシリティ3を使います)。 |
|  6 | lpr        | ラインプリンターサブシステム (旧式のサブシステム)     |                                                                                       |
|  7 | news       | ネットワークニュースサブシステム (旧式のサブシステム) |                                                                                       |
|  8 | uucp       | UUCP サブシステム (旧式のサブシステム)                |                                                                                       |
|  9 |            | クロックデーモン                                      | systemd-timesyncd                                                                     |
| 10 | authpriv   | セキュリティ/認証メッセージ                           | ファシリティ4もあります。                                                             |
| 11 | ftp        | FTP デーモン                                          |                                                                                       |
| 12 | -          | NTP サブシステム                                      |                                                                                       |
| 13 | -          | log audit                                             |                                                                                       |
| 14 | -          | log alert                                             |                                                                                       |
| 15 | cron       | スケジューリングデーモン                              |                                                                                       |
| 16 | local0     | ローカル使用 0 (local0)                               |                                                                                       |
| 17 | local1     | ローカル使用 1 (local1)                               |                                                                                       |
| 18 | local2     | ローカル使用 2 (local2)                               |                                                                                       |
| 19 | local3     | ローカル使用 3 (local3)                               |                                                                                       |
| 20 | local4     | ローカル使用 4 (local4)                               |                                                                                       |
| 21 | local5     | ローカル使用 5 (local5)                               |                                                                                       |
| 22 | local6     | ローカル使用 6 (local6)                               |                                                                                       |
| 23 | local7     | ローカル使用 7 (local7)                               |                                                                                       |

## logrotate

ログのローテーションの設定は `/etc/logrotate.conf`, `/etc/logrotate.d/` に記述します。
Amazon Linux 2 の場合は `cat /var/lib/logrotate/logrotate.status` でローテート状況を確認できます。
logrotate 処理は cron で呼び出されていて `/etc/cron.daily/logrotate` に処理内容が記述されています。
`man logrotate` でマニュアルが確認できます。

### 設定ファイルのチェック方法
`logrotate -d [filename]` で確認します。

### 設定例
```conf
/var/log/cron
/var/log/maillog
/var/log/messages
/var/log/secure
/var/log/spooler
{
    missingok
    sharedscripts
    postrotate
        /bin/kill -HUP `cat /var/run/syslogd.pid 2> /dev/null` 2> /dev/null || true
    endscript
}
```

### 設定の内容
よく使われそうな設定を記述しておきます。

| 設定      |                          説明                         |
|:---------:|:-----------------------------------------------------:|
| hourly | 一時間ごとにローテートします。ただし、これを有効にするためには、logrotate 自体の cron 呼び出し頻度を調整する必要がある場合があります。 | 
| daily | 毎日ログをローテートします。|
| weekly [weekday] | ログを一週に一度ローテートします。weekday は 0-7 を指定し、0 の日曜日から 6 の土曜日をあらわします。7 は曜日に関わらず一週間ごとにローテートします。  |
| monthly | 一ヶ月ごとにローテートします。 |
| rotate | 指定した回数までローテートし、その後削除または mail されます。 |
| notifempty | ログファイルがからの場合はローテートしません。 |
| compress | ローテートされたログは圧縮します。デフォルトでは gzip 形式になります。 |
| missingok | ログファイルが見つからない場合に、エラーを出さずに処理を継続します。 |
| sharedscripts | prerotate, postrotate がローテートされたそれぞれのファイルに呼び出されるのか、一度に呼び出されるのか設定します。 |
| postrotate/endscript | postrotate と endscript の間の内容が /bin/sh として実行されます。 |

SIGNAL については `man 7 signal` で概要を確認できます。
ただ、実際のところ rsyslogd の場合は、シグナルをトラップして独自実装をしているみたいです（ちゃんと追っているわけではないですが）。

* [sighup の handler](https://github.com/rsyslog/rsyslog/blob/ee1485be7d6ab8f2d4b07074ecd6efac491cd1c5/tools/rsyslogd.c#L1231)
* [HUP の処理をしていると思われる箇所](https://github.com/rsyslog/rsyslog/blob/ee1485be7d6ab8f2d4b07074ecd6efac491cd1c5/tools/rsyslogd.c#L1758)