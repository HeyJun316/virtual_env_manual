# virtual_env_manual
# バージョン一覧
- OS
    - macOS Catalina10.15.7
- VirtualBox
    - 6.0.14
- PHP
    - 7.3
- MySQL
    - 5.7
- Laravel
    - 6.0
- Server
    - Nginx

## VirtualBoxのインストール
1. 公式サイトから、dmgファイルをダウンロード
> https://www.virtualbox.org/wiki/Download_Old_Builds_6_0
2. OS X hostsを選択し下記のコマンドを実行すると、インストールされる
> $virtualbox
## Vagrantのインストール
1. 下記のコマンドでVagrantをインストール。
> $brew cask install vagrant
> もしくは
> $brew install vagrant --cask


2. インストールが終了したら、Vagarantコマンドが使用可能か確認。
> $vagrant -v


## Vagrant boxダウンロード＋設定編集
1. 下記のコマンドを実行し、CentOSバージョン7ダウンロードする。
> $vagrant box add centos/7
>
> *ディレクトリはどこでもOK

2. 実行すると4つの選択肢が表示されるので、3番目の選択してenterを押す。
> 1) hyperv
> 2) libvirt
> 3) virtualbox
> 4) vmware_desktop

3. Vagrant作業用ディレクトリを作成・移動し、下記のコマンドを実行
> [コマンド]
> $mkdir vagrant_test && cd vagrant_test && vagrant init centos/7
---
> [問題なければ以下の文言が表示される]
>
>A `Vagrantfile` has been placed in this directory. You are now
ready to `vagrant up` your first virtual environment! Please read
the comments in the Vagrantfile as well as documentation on
`vagrantup.com` for more information on using Vagrant.

>
4. 下記のコマンドを実行しVagrantfile内の3箇所を編集
>[Vagrantfile外]
>
> $vi vagrantfile
>
---
>
>
> [Vagrantfile内]
>
> 変更⑴ 26行目
> アンコメント
> config.vm.network "forwarded_port", guest: 80, host: 1234
>
> 変更⑵ 35行目
> アンコメント + ipを33.10→33.18
> config.vm.network "private_network", ip: "192.168.33.18"
>
> 変更点⑶ 46行目
> config.vm.synced_folder "../data", "/vagrant_data"
> ↓ 以下に編集
> config.vm.synced_folder "./", "/vagrant", type:"virtualbox"

## Vagrantを使用しゲストOS起動とログイン
1. 起動コマンド
> [コマンド]
> $vagrant up
---
> [もし、ホストOSとゲストOSのフォルダを同期できないエラー発生した場合]
> No package kernel-devel-3.10.0-1127.el7.x86_64 available.
>
> The following SSH command responded with a non-zero exit status.
Vagrant assumes that this means the command failed!
>
> umount /mnt
> Stdout from the command:
> Stderr from the command:
> umount: /mnt: not mounted
>
>1. vagrant内で下記のコマンドを実行
>
> $sudo yum -y update kernel
>
>2. exitで出て下記のコマンドを実行
> $vagrant reload --provision


2. ログインコマンド
> $vagrant ssh



## 開発に必要なパッケージをインストール
1.下記のコマンド実行で、vagrant内でのグループパッケージのインストールにより、開発に必要なパッケージを一括ダウンロードが可能になる
> $sudo yum -y groupinstall "development tools"

## PHPのインストール(7.3)
laravelを動作させるには、PHPのバージョン7以上が必要

1. 外部パッケージツールからPHPインストール
> $sudo yum -y install ep
2. wgetでhttpのファイルをダウンロード
> $sudo wget http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
3. remiのパッケージリポジトリをインストール
> $sudo rpm -Uvh remi-release-7.rpm
4. PHPのインストール
> $sudo yum -y install --enablerepo=remi-php73 php php-pdo php-mysqlnd php-mbstring php-xml php-fpm php-common php-devel php-mysql unzip
5. PHPのversion確認
> $php -v

## Composerのインストール
1.セットアップ用のPHPファイルをダウンロード
> $php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
2. ダウンロードファイルの実行
> $php composer-setup.php
3. composer-set.phpの削除1
> $php -r "unlink('composer-setup.php');"
4. composer.pharを移動させ、どのディレクトリでもcomposerコマンドを使用可能にする
> $sudo mv composer.phar /usr/local/bin/composer
5. composerのバージョン確認
> $composer -v

## Nginx インストール
1. vagrant内で下記のコマンドでファイルを作成し、viで書き込む内容を記述し保存
> [コマンド]
> $sudo vi /etc/yum.repos.d/nginx.repo
---
> [nginx.repo内記述内容]
> name=nginx repo
> baseurl=https://nginx.org/packages/mainline/centos/\$releasever/\$basearch/
> gpgcheck=0
> enabled=1

2.下記のコマンドでNginxをインストール
> $sudo yum install -y nginx

3. Nginxのversion確認
> $nginx -x

4. Nginxの起動
> $sudo systemctl start nginx

5. Nginxの状態確認
> $sudo systemctl status nginx

6. ブラウザにて http://192.168.33.18
を入力して動いてるか確認

7. もし動かなければexitで外に出て、vagrant reloadと、Vagrantfileのポートを確認

## DBのインストール(MySQL5.7)
1. wgetでhttpのファイルをインストール
> $sudo wget https://dev.mysql.com/get/mysql57-community-release-el7-7.noarch.rpm
2. rpmに新しくリポジトリを追加しインストール
> $sudo rpm -Uvh mysql57-community-release-el7-7.noarch.rpm

3. YumRepositoryからパッケージをインストール
> $sudo yum install -y mysql-community-server

4. バージョン確認
> mysql --version

5. 起動
> $sudo systemctl start mysqld

6.  MySQLの状態確認
> $sudo systemctl status mysqld

## MySQLのパスワード設定
1. mysqld.logのtemporary パスワードを取得
> $sudo cat /var/log/mysqld.log | grep 'temporary password'

2. my.cnfを編集
> $sudo vi /etc/my.cnf
---
> [追加記述]
>
> validate-password=OFF

3. MySQLサーバーを再起動する
> $sudo systemctl restart mysqld

4. 取得したtemporary passwordでログイン
> mysql -u root -p;

5. パスワード変更
> set password = 'new pass';

6. DB作成
> create database laravel_test;

## Laravelのインストール(6.0)
1. ホストOSのvagrant_test下で下記のコマンドを実行すると、[laravel_test]という名前で新しくディレクトリが作成される
> $composer create-project --prefer-dist laravel/laravel laravel_test "6.0.*"
>
## LaravelとNginxを同期
1. default.conf編集しNginx同期設定
> [コマンド]
> $sudo vi /etc/nginx/conf.d/default.conf
----
> [default.conf編集]
> server {
  listen       80;
⑴server_name
  server_name  192.168.33.18;
⑵rootとindexを追記
root /vagrant/laravel_test/public;
  index  index.html index.htm index.php;
>  ⑶コメントアウトとtyrfilesを追記
>  location / {
      #root   /usr/share/nginx/html; # コメントアウト
      #index  index.html index.htm;  # コメントアウト
      try_files \$uri \$uri/ /index.php$is_args\$&args;
      # 追記
  }
> ⑷アンコメントとfastcgi_param変更
> location ~ \.php$ {
      fastcgi_pass   127.0.0.1:9000;
      fastcgi_index  index.php;
      fastcgi_param  SCRIPT_FILENAME  /\$document_root/$fastcgi_script_name;
      include        fastcgi_params;
  }

2. php-fpm設定
> [コマンド]
> $sudo vi /etc/php-fpm.d/www.conf
---
> [www.conf編集]
> user = apache
> ↓ 以下に編集
> user = vagrant
>
> group = apache
> ↓ 以下に編集
> group = vagrant

4. SELinuxの設定を変更
> [コマンド]
> $sudo vi /etc/selinux/config
---
> [config編集]
> SELINUX=disabled

5. vagrantを再起動
> $vagrant reload

## LaravelとMySQLを同期
1. .envファイル内でMySQLと同期設定
> [コマンド]
> $vi .env
>
---
> [.env内]
> DB_DATABASE='作成したDB名'
> DB_PASSWORD='登録したパス'

2.laravel_testディレクトリ下でマイグレーションを実行してテーブル定義
> $php artisan migrate


## Laravel ログイン機能
1. laravel_test下で、下記のコマンドを入力で、認証に必要な機能を準備してくれる
> $composer require laravel/ui "^1.0" --dev && php artisan ui vue --auth

2. 権限を与えてあげて、ログファイルへの書き込みができるようにする
> $cd /vagrant/laravel_test && sudo chmod -R 777 storage


# 環境構築の所感
- vi で操作する際[x]で一文字削除
- Vagrantfile内のport番号がかぶるとエラーが出るので、被らないようなport設定
- Remi-releaseで自己責任だがPHPの最新ver.をインストールできる(EPELが必要だが)
- php -r の -rはPHPのスクリプトタグなしでPHPコードを実行するオプション
- SELinuxをenforcingをdisabledに変更を忘れていた
- 権限についての理解が深まったが、詳しいことは理解できてない。



# 参考サイト
- wget http://usmysa.hatenablog.com/entry/2016/05/24/223311
- PHP7.3
 https://www.rem-system.com/centos-httpd-php73/
- Laravel
https://readouble.com/laravel/6.x/ja/installation.html
- Laravel認証
https://readouble.com/laravel/6.x/ja/authentication.html?header=%25E3%2582%25A4%25E3%2583%2599%25E3%2583%25B3%25E3%2583%2588
- SELinux
https://eng-entrance.com/linux-selinux
- パーミッションについて
https://gakumon.tech/nginx/nginx_permission_conclusion.html