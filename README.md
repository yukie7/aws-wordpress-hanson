# VPCの作成
1. VPC ダッシュボードに移動
2. 以下の設定でVPCを作成

|名前|IPv4|
|---|---|
|hanson-user1|10.0.0.0/16|

# パブリックサブネットの作成
1. 以下の設定でサブネットを作成

|名前|VPC|アベイラビリティーゾーン|IPv4|
|---|---|---|---|
|パブリックサブネット|hanson-user1|ap-northeast-1a|10.0.1.0/24|

# インターネットゲートウェイの作成
1. 以下の設定でインターネットゲートウェイを作成

|名前|
|---|
|hanson-user1-igw|

2. hanson-user1-igwを選択
3. アクションからVPCにアタッチを選択
4. hanson-user1にアタッチ

# パブリックルートテーブルの作成
1. 以下の設定でルートテーブルを作成

|名前|VPC|
|---|---|
|パブリックルートテーブル|handson-user1|

2. パブリックルートテーブルをクリック
3. 画面下部のSubnet Associationsタブをクリック
4. Edit subnet associationsをクリック
5. パブリックサブネットを選択してSave
6. 画面下部のルートタブを選択してEdit routesを選択
7. 以下のルートを追加してSave routesをクリック

|Destination|Target|
|---|---|
|0.0.0.0/0|hanson-user1-igw|


# Webサーバーの作成
1. EC2 ダッシュボードに移動してインスタンスの作成をクリック
2. AMIはAmazon Linux 2 AMI (HVM), SSD Volume Typeを選択
3. インスタンスタイプはt2.microが選択されていることを確認して次の手順へ
4. インスタンスの詳細設定以下の設定を変更して次の手順へ

|ネットワーク|サブネット|自動割り当てパブリックIP|プライマリIP|
|---|---|---|---|
|handson-user1|パブリックサブネット|有効|10.0.1.10|

5. ストレージの追加はデフォルトのまま次の手順へ
6. タグの追加は以下の通り設定して次の手順へ

|キー|値|
|---|---|
|Name|Webサーバー|

7. セキュリティグループの設定ではセキュリティグループ名をWEB-SGに設定して確認と作成をクリック
8. 設定を確認して起動をクリック
9. 新しいキーペアの作成を選択してキーペア名にmy-keyと入力
10. 秘密鍵ファイルをダウンロードしてインスタンスの作成をクリック
11. 作成に成功したらインスタンスの表示をクリック

# SSHで接続確認
1. EC2のインスタンス一覧からWebサーバーを選択
2. 画面下部の説明タブにあるIPv4 パブリック IPをコピーしておく
3. Tera Termを起動
4. ホストに2.でコピーしたパブリックIPを入力しサービスにSSHを選択した状態でOKをクリック
5. セキュリティ警告が出た場合は続行をクリック
6. SSH認証画面が出たらユーザ名にec2-userと入力
7. RSA/DSA/ECDSA/ED25519鍵を使うをクリック
8. 秘密鍵ボタンをクリック
9. すべてのファイルを選択してダウンロードした秘密鍵ファイルを選択して開くをクリック
10. OKをクリック
11. 接続が確認出来たら成功

# Apache HTTP Serverのインストール
1. 以下のコマンドを入力してApacheをインストール

```$ sudo yum -y install httpd```

2. 以下のコマンドを入力してApacheを起動

```$ sudo service httpd start```

3. 以下のコマンドを入力してサーバー起動時にApacheも自動的に再起動するように設定

```$ sudo chkconfig httpd on```

4. 以下のコマンドを入力する

```ps -ax | grep httpd```

以下の行が表示されていればサーバー上でApacheが動作している

```12134 ? Ss 0:00 /user/sbin/httpd```

6. 以下のコマンドでネットワークの待ち受け状態を確認

```sudo lsof -i -n -P```

httpdの行にTCP *:80と表示されていることを確認

# ファイアウォールの設定
1. セキュリティグループを表示
2. WEB-SGを選択
3. 画面下部のインバウンドをクリック
4. ルールの編集をクリック
5. ルールの追加をクリック
6. 以下の設定でルールを追加してルールの保存をクリック

|タイプ|プロトコル|ポート範囲|ソース|
|---|---|---|---|
|カスタム TCP ルール|80|0.0.0.0/0|

7. ブラウザからWebサーバのパブリックIPにアクセス
8. Apacheのテストページが表示されたら成功

# DNSの設定
1. VPC ダッシュボードに移動
2. hanson-user1を選択しアクションからホスト名の編集をクリック
3. DNSホスト名を有効にして保存
4. EC2 ダッシュボードに移動
5. Webサーバーを選択
6. 画面下部の説明のタブのパブリック DNS (IPv4)に値が設定されていることを確認

## プライベートサブネットの作成
1. VPC ダッシュボードに移動
2. 以下の設定でサブネットを作成

|名前|VPC|AZ|IPv4|
|---|---|---|---|
|プライベートサブネットサブネット|hanson-user1|ap-northeast-1a|10.0.2.0/24|

# プライベートルートテーブルの作成
1. 以下の設定でルートテーブルを作成

|名前|VPC|
|---|---|
|プライベートルートテーブル|handson-user1|

# DBサーバーの作成
1. EC2 ダッシュボードに移動
2. 以下の設定でインスタンスを作成、記述がない設定はWebサーバー作成時と同じ設定

|ネットワーク|サブネット|自動割り当てパブリックIP|プライマリIP|
|---|---|---|---|
|handson-user1|プライベートサブネット|有効|10.0.2.10|

3. タグの追加は以下の通り設定して次の手順へ

|キー|値|
|---|---|
|Name|DBサーバー|

4. セキュリティグループの設定ではセキュリティグループ名をDB-SGに設定
5. ポート3306、ソースが任意の場所のルールを追加

# pingコマンドで疎通確認
1. セキュリティグループDB-SGに以下のインバウンドルールを追加

|タイプ|ソース|
|---|---|
|すべてのICMP - IPV4|任意の場所|

2. Tera TermでWebサーバーにログイン
3. 以下のコマンドでDBサーバーにpingを実行

```$ ping 10.0.2.10```

4. WEB-SGに1.と同じセキュリティグループを設定
5. ローカル環境からWebサーバーのパブリックIPにpingが実行できることを確認

# Webサーバー経由でDBサーバーにSSH接続
1. Tera TermのファイルメニューからSSH SCPをクリック
2. Fromに秘密鍵ファイルを選択、Toに~/を入力してSendをクリック
3. Webサーバーのホームディレクトリに秘密鍵ファイルがコピーされていることを確認
4. 以下のコマンドで秘密鍵ファイルのパーミッションを変更

```$ chmod 400 my-key.pem```

5. 以下のコマンドでWebサーバーを踏み台にしてDBサーバーに接続

```$ ssh -i my-key.pem ec2-user@10.0.2.10```

6. Are you sure you want to continue connecting (yes/no)? が表示されたらyesを入力
7. DBサーバーにログインできたら成功
8. exitコマンドまたはCtrl+Dでログアウト

# NATゲートウェイを作成
1. VPC ダッシュボードに移動
2. NAT ゲートウェイを以下の設定で作成

|サブネット|Elastic 割り当て ID|
|---|---|
|パブリックサブネット|新しいEIPの作成|

3. NAT ゲートウェイ IDをコピーしておく
4. VPC IDがhanson-user1のルートテーブルのうち、メインがYesになっているルートテーブルを選択
5. 以下のルートを追加

|Destination|Target|
|---|---|
|0.0.0.0/0|3.でコピーしたNAT ゲートウェイ ID|

6. Webサーバー経由でDBサーバーにSSH接続
7. 以下のコマンドを入力してHTMLが取得できればNATゲートウェイが機能しHTTPで通信できていることが確認できる


# DBサーバーにMySQLをインストール
1. Webサーバー経由でDBサーバーにSSH接続
2. 以下のコマンドでMySQLをインストール

```$ sudo yum -y install mysql-server```

3. インストールに失敗する場合は以下のコマンドを順に実行

```
$ sudo yum remove mariadb-libs
$ sudo rm -rf /var/lib/mysql/
$ sudo yum install http://dev.mysql.com/get/mysql57-community-release-el7-11.noarch.rpm
$ sudo yum -y install mysql-server
```

4. 以下のコマンドでMySQLの起動と初期設定を行う

```
$ sudo service mysqld start
$ sudo mysqladmin -u root password
New password: 新パスワード
Confirm new password: 同じパスワードを入力
```

5. 以下のコマンドでMySQLに接続

```$ mysql -u root -p```

6. 以下のコマンドでデータベースを作成

```mysql> CREATE DATABASE wordpress DEFAULT CHARACTER SET utf8 COLLATE utf8_general_ci;```

7. wordpressユーザーを作成、パスワードはwordpresspasswdとする

```mysql> grant all on wordpress.* to wordpress@"%" identified by 'wordpresspasswd';```

エラーが出るときは以下のコマンドを実行

```
$ mysql -u root -p
mysql> flush privileges;
mysql> grant all on wordpress.* to wordpress@"%" identified by 'wordpresspasswd';
```

8. DBサーバーからログアウト

# WebサーバーにWordPressをインストール
1. Webサーバーにログイン
2. 以下のコマンドでPHPやMySQLのライブラリをインストール

```
$ sudo yum -y install php php-mysql php-mbstring
$ sudo yum -y install mysql
$ mysql -h 10.0.2.10 -u wordpress -p
mysql> exit
```

3. WordPressをダウンロード

```
cd ~
wget http://ja.wordpress.org/latest-ja.tar.gz
```

ダウンロード後、以下のコマンドで展開およびインストール

```
tar xzvf latest-ja.tar.gz
cd wordpress
sudo cp -r * /var/www/html/
sudo chown apache:apache /var/www/html -R
```

# WordPressを設定
1. 以下のコマンドでApacheの起動

```sudo service httpd start```

起動中の場合は再起動

```sudo service httpd restart```

2. ブラウザでWebサーバーのパブリックIPまたはDNS名にアクセス
3. WordPressの画面に従い初期設定、データベース情報は以下の通り入力

|データベース名|ユーザー名|パスワード|データベースのホスト名|テーブル接頭辞|
|---|---|---|---|---|
|wordpress|wordpress|wordpresspasswd|10.0.2.10|wp_（デフォルト値）|

4. WordPressの管理ページは/wp-admin/

# 後片付け
1. EC2インスタンスを停止
2. NATゲートウェイを削除
3. メインルートテーブルのルートからNATゲートウェイの設定を削除
4. EIPを解放

