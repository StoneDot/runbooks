Linux では各ユーザーがリソースを食い潰さないようにするために、いくつかのセキュリティー関係の機能が使用できます。

# real user
real user ごとに利用可能なリソースを制限することができます。
Linux の System call としては getrlimit, setrlimit が用意されています。
それぞれ現在のハードリミットとソフトリミットの取得、設定が可能です。
ソフトリミットは非特権ユーザーも変更可能な数値で、 ハードリミットは特権ユーザーのみが変更可能な数値になっています。。

以下のような項目が設定できます。

* RLIMIT_AS: プロセッサの仮想メモリとして確保できるメモリのバイト数。
* RLIMIT_CORE: Core dump 作成時に作成される最大バイト数。
* RLIMIT_CPU: 最大で使用することができる CPU time。リソースを使い切ると SIGXCPU が送られる。
* RLIMIT_DATA: プロセッサで使用できる最大のデータセグメントのサイズ。
* RLIMIT_FSIZE: 
* RLIMIT_LOCKS: 
* RLIMIT_MEMLOCK: 
* RLIMIT_MSGQUEUE (Since Linux 2.6.8): 
* RLIMIT_NICE (since Linux 2.6.12, but see BUGS below): 
* RLIMIT_NOFILE: 
* RLIMIT_RSS: 
* RLIMIT_RTPRIO (Since Linux 2.6.12, but see BUGS): 
* RLIMIT_RTTIME (Since Linux 2.6.25): 
* RLIMIT_SIGPENDING (Since Linux 2.6.8): 
* RLIMIT_STACK: 

詳細については man getrlimit や man setrlimit から参照してください。

