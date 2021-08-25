# Amazon Linux 2
Amazon Linux 2 は AWS が提供している Linux の Distribution です。
通常 EC2 上で使われることが多いので、EC2 での動作を前提に記述します。
もし、EC2 以外では動作が異なる可能性がある場合は、適宜その旨を注記を挿入します。

## 基本アーキテクチャ
RHEL7 系と同様のソフトウェアが使われています。
たとえば `init` の代わりに `systemd` が使用されており、パッケージ管理については `yum` コマンドをベースに行われます。

## Systemd
Systemd では設定はユニットファイルで管理されます。
そのため、以下のコマンドを使ってユニットファイルを一覧することで、どのようなソフトウェアがインストールされているのかを確認できます。

```bash
$ sudo systemctl list-unit-files | head -3
UNIT FILE                                     STATE
proc-sys-fs-binfmt_misc.automount             static
dev-hugepages.mount                           static
```

## RPM Repository
Amazon Linux 2 では [Knowledge Center の記事](https://aws.amazon.com/jp/premiumsupport/knowledge-center/ec2-al1-al2-update-yum-without-internet/) に記載があるように、RPM パッケージは S3 上にホストされています。
そのため、インターネットへのアクセスがない場合でもパッケージの更新は可能です。

Amazon Linux 2 のセキュリティーパッチや CVE 対応については、[ALAS (Amazon Linux Security Center)](https://alas.aws.amazon.com/alas2.html) から参照できます。

## Systems Manager Agent
Amazon Linux 2 の最新の AMI では amazon-ssm-agent.service にて、AWS Systems Manager Agent がインストールされています。
これは AWS の Systems Manager と連携するために必要なサービスです。
詳細については [SSM Agent の公式ページ](https://docs.aws.amazon.com/systems-manager/latest/userguide/ssm-agent.html) を参照してください。

Systems Manager ではリモートでコマンドの実行や、作業のオートメーションなど便利な点も多いです。
その一方、以下のように運用上の問題が発生する可能性に、注意が必要です。

* メモリの少ない t3.nano などの EC2 インスタンスでは、ドキュメント AWS-RunPatchBaseline の処理により OOM が発生する。
* 自動的に実行される処理により CPU 使用率の高騰が発生し、ワークロードに悪影響を与える。
* Systems Manager に関係する IAM などの不適切な権限設定に伴い、インスタンス内部の情報が流出、改ざんされる。
* 一時ファイルのクリーンアップが行われず、ディスクの枯渇が発生する。
* Systems Manager の自動アップデートに失敗する。

そのため、Systems Manager に対しての理解が浅い状況の場合は、Systems Manager 有効化での十分な試験運用を行い、事前に問題の洗い出しを行う必要があります。

以下では EC2 インスタンスレベルでの事前対応が必要になる、一時ファイルのクリーンアップについて記載します。

### 一時ファイルのクリーンアップ

## CloudWatch Agent

## cloud-init

