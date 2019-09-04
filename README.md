# redmine-ubuntu-ansible
## Redmine4.X unofficialcooking edition. (Use Ubuntu,PostgreSQL)

最小構成でインストールしたUbuntu ServerにRedmineを自動インストールするためのAnsibleプレイブックです。

コマンド6個をコピペ実行するだけで、あとはしばらく放置すればインストールが完了します。


## 概要

Ansibleを使ってRedmineを自動インストールするためのプレイブックです。以下のwebサイトで紹介されている手順におおむね準拠しています。

[Redmine 3.4をUbuntu Server 16.04.2 LTSにインストールする手順](http://blog.redmine.jp/articles/3_4/install/ubuntu/)

Redmine標準に対して、カテゴリのサブプロジェクト継承パッチの組み込み、プラグイン、テーマの追加、admin初期パスワードの変更などを行っています。

## Redmine標準からの変更内容

### カテゴリのサブプロジェクト継承機能を追加しました。（標準のバージョンと同じ 2019/06/30） ###

https://github.com/y503unavailable/redmine/issues/14

http://www.redmine.org/issues/5358#note-108

Redmine標準からの変更内容は下記参照ください。

取り込んだ機能はRedmine.TokyoのUnofficialCookingで説明しています。

https://github.com/y503unavailable/redmine/blob/3.4-unofficialcooking/README.rdoc     （未作成）

https://redmine.tokyo/projects/unofficialcooking

## 同時インストールするプラグイン

redmine_startpage, redmine_issue_templates, redmine_banner, redmine_view_customize, redmine_xlsx_format_issue_exporter, redmine_theme_changer, redmine_default_custom_query, redmine_message_customize

詳細は下記参照ください。

https://github.com/y503unavailable/redmine-ubuntu-ansible/blob/master/roles/redmine-plugins/tasks/main.yml

## 同時インストールするテーマ

farend_basic,redmine_flat,gitmike,PurpleMine2,minimalflat2,flatly_light,kodomo,farend_bleuclair

詳細は下記参照ください。

https://github.com/y503unavailable/redmine-ubuntu-ansible/blob/master/roles/redmine-themes/tasks/main.yml

## admin初期パスワードの変更

Redmineインストール直後のadmin初期パスワードは admin で固定されており、インストール直後に乗っ取られる可能性を否定できません。（特にインターネット上VPS等を利用する場合）

そのため、情報セキュリテイ対策として、admin初期パスワードを変更しました。初期パスワードは必要に応じ変更ください。

admin初期パスワード  unofficial-cracking


## システム構成

* Redmine 4.0
* Ubuntu Server 18.04 LTS
* PostgreSQL
* Apache


## Redmineのインストール手順

インストール直後の Ubuntu 18.04 にログインし以下の操作を行ってください。

（自動更新(unattend)が完了するまで数分待ってから実行）

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

ubuntu16または19を利用する場合は、下記箇所を修正してからansibleを実行してください。

roles/system/tasks/main.yml

```
name='postgresql,postgresql-server-dev-10,python-psycopg2'
```

```
ubuntu16の場合 postgresql-server-dev-9.5
ubuntu18の場合 postgresql-server-dev-10  (初期値)
ubuntu19の場合 postgresql-server-dev-11
```

### playbook実行

下記コマンドを実行してください。Redmineの自動インストールが開始されます。

```
cd redmine-ubuntu-ansible
ansible-playbook -i hosts site.yml
```

10〜20分ほどでインストールが完了します。

webブラウザで `http://サーバIPアドレス/redmine` にアクセスしてください。Redmineの画面が表示されるはずです。

初期パスワードは admin/ unofficial-cracking です。（標準から変更）

---

## 初期設定の変更

初期設定を変更する場合は、ダウンロードしたプレイブック内の下記ファイル を、インストール前に変更してください。

### Redmine admin 初期パスワードの変更

admin初期パスワードを変更する場合は、下記箇所を変更してから実行ください。

group_vars/redmine-servers

```
redmine_admin_passwd: unofficial-cracking
```

### Redmine 初期テーマの変更

初期設定されるテーマを変更する場合は、下記箇所を変更してから実行ください。

インストールされるテーマの一覧は、Redmineインストール下の/public/themes/を参照ください。

group_vars/redmine-servers

```
redmine_default_theme: redmine_flat
```


### Redmineオリジナルで利用したい場合

下記箇所を変更してから実行ください。（2018/3現在）

group_vars/redmine-servers

#### Redmine-本家gitミラー

```
redmine_git_url: https://github.com/redmine/redmine.git
redmine_git_branch: 4.0-stable
```

### PostgreSQLに設定するパスワードの変更 (推奨)

ファイル `group_vars/redmine-servers` をエディタで開き、 `db_passwd_redmine` を適当な内容に変更してください。

これはPostgreSQLのRedmine用ユーザー redmine に設定されるパスワードです。

---

## 起動後の設定変更

### kodomo テーマで利用したい場合

起動後、kodomoテーマを選択し、 message_customizeプラグインで用語を変更してください。
https://github.com/akiko-pusu/redmine_theme_kodomo

## ライセンス

MIT License

## 作者

y503unavailable （Redmine.Tokyoスタッフ unofficial redmine cooking 担当）

原作
[ファーエンドテクノロジー株式会社](http://www.farend.co.jp/)
