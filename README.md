# redmine-ubuntu-ansible
## Redmine4.X unofficialcooking edition. (Use Ubuntu,PostgreSQL)

最小構成でインストールしたUbuntu ServerにRedmineを自動インストールするためのAnsibleプレイブックです。

コマンド6個をコピペ実行するだけで、あとはしばらく放置すればインストールが完了します。


## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.4をUbuntu Server 16.04.2 LTSにインストールする手順](http://blog.redmine.jp/articles/3_4/install/ubuntu/)

Redmine本家5358に投稿したカテゴリのサブプロジェクト継承パッチを組み込んでいます。
http://www.redmine.org/issues/5358#note-108

## システム構成

* Redmine 4.0
* Ubuntu Server 16.04.2 LTS
* PostgreSQL
* Apache


## Redmineのインストール手順

インストール直後の Ubuntu 16.04 にログインし以下の操作を行ってください。
（自動更新が完了するまで数分待ってから実行）

### Ansibleとgitとunzipのインストール

```
sudo apt-get update
sudo apt-get install -y python-pip libpython-dev git libssl-dev unzip
sudo pip install ansible
```

### playbookのダウンロード

```
git clone https://github.com/y503unavailable/redmine-ubuntu-ansible.git
```

### PostgreSQLに設定するパスワードの変更 (推奨)

ファイル `group_vars/redmine-servers` をエディタで開き、 `db_passwd_redmine` を適当な内容に変更してください。

これはPostgreSQLのRedmine用ユーザー redmine に設定されるパスワードです。

### ubuntuのバージョンによる変更点

ubuntuのバージョンにより、デフォルトのpostgresqlのバージョンが異なります。

ubuntu18または19を利用する場合は、下記箇所を修正してからansibleを実行してください。

roles/system/tasks/main.yml

```
name='postgresql,postgresql-server-dev-9.5,python-psycopg2'
```

```
ubuntu16の場合 postgresql-server-dev-9.5  (初期値)
ubuntu18の場合 postgresql-server-dev-10
ubuntu19の場合 postgresql-server-dev-11
```

### playbook実行

下記コマンドを実行してください。Redmineの自動インストールが開始されます。

```
cd redmine-ubuntu-ansible
ansible-playbook -K -i hosts site.yml
```

10〜20分ほどでインストールが完了します。

webブラウザで `http://サーバIPアドレス/redmine` にアクセスしてください。Redmineの画面が表示されるはずです。


## ライセンス

MIT License


## 作者

[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/)
