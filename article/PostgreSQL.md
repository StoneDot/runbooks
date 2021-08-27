# PostgreSQL 13 について

コードリーディングをして PostgreSQL の理解を深めたいなと思ったので、環境を準備しておきたいと思います。
Amazon Linux 2 に実際の環境を作り、手元の CLion でソースを読む状況が目標です。

## Amazon Linux 2 でのインストール

通常は以下のパッケージをインストールすることになると思います。

```shell
sudo amazon-linux-extras enable postgresql13
sudo yum clean metadata
sudo yum install postgresql postgresql-server
```

私はソースコードやドキュメントを確認したかったので、以下のパッケージも追加でインストールします。

```shell
sudo yum install postgresql-server-devel postgresql-devel
sudo yum install postgresql-docs
```

このままだと HTML ドキュメントを確認する方法がないので、Nginx でホストします。

```shell
sudo amazon-linux-extras enable nginx1
sudo yum clean metadata
sudo yum install nginx
```

`/etc/nginx/nginx.conf` の以下の箇所を修正します。

```conf
    server {
        # ポートは 8080 に変更し、ローカルホストからの接続のみ受け付けます
        listen       127.0.0.1:8080;
        listen       [::1]:8080;
        server_name  _;
        # この root 部分を修正します。
        root         /usr/share/doc;
        # autoindex を有効化し、ファイルを探しやすくしまsう。
        autoindex on;
        autoindex_exact_size off;
        autoindex_format html;


        # Load configuration files for the default server block.
        include /etc/nginx/default.d/*.conf;

        error_page 404 /404.html;
        location = /404.html {
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
        }
    }
```

nginx を起動させ、再起動時にも自動で起動するようにしておきます。

```shell
sudo systemctl status nginx
sudo nginx -t
sudo systemctl start nginx
sudo systemctl status nginx
curl localhost:8080/postgresql-docs-13.3/html/
sudo systemctl enable nginx
```

とりあえず ssh したときに、localhost から取得できるように以下の設定を `.ssh/config` に入れておきます。
`localhost:8080` は衝突しやすいため、`localhost:8082` でアクセスできるようにします。

```conf
Host db
  HostName <snipped>
  Port 22
  User ec2-user
  IdentityFile <snipped>
  LocalForward 8082 localhost:8080
  ServerAliveInterval 30
  ProxyCommand ssh -W %h:%p <bastion>
```

これで ssh 越しで PostgreSQL のドキュメントが読めて便利です。
[PostgreSQL 13.3 のドキュメント](http://localhost:8082/postgresql-docs-13.3/html/) のリンクを残しておきます。

ちなみに `/usr/lib64/pgsql/tutorial/` にはチュートリアルスクリプトが置いてあります。

## CLion の環境
CLion を使って、ソースを眺めたり独自バージョンをビルドできる環境を作っていきます。

https://www.jetbrains.com/help/clion/remote-projects-support.html