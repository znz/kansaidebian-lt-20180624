# systemdでよく使うサブコマンド

author
:   Kazuhiro NISHIYAMA

content-source
:   第136回関西Debian勉強会 + Linux Kernel 勉強会 LT大会

date
:   2018/06/24

allotted-time
:   10m

theme
:   lightning-simple

# self.introduce

- One of Ruby committers
- Twitter, GitHub: `@znz`

# 基本コマンド

- systemctl start Unit名
- systemctl stop Unit名
- systemctl restart Unit名
- systemctl status Unit名

# たまに使うコマンド

- systemctl enable Unit名
- systemctl disable Unit名

Debian 系に慣れているとパッケージインストールで即有効担っていることが多いので忘れがち

# 一覧コマンド

- systemctl list-units
- systemctl list-timers

# systemd の設定ファイルの場所

- /lib, /usr/lib 以下: パッケージが使う (例: /lib/systemd/system, /usr/lib/tmpfiles.d)
  - 普通はいじらない
- /etc 以下: 変更するファイル (例: /etc/systemd/system, /etc/tmpfiles.d)
  - 同名ファイルで完全に上書き
  - Unit名.d/なんとか.conf で追加変更
- /run 以下: 動的に生成されるファイル (例: /run/systemd/system, /run/tmpfiles.d)
  - init.d 以下から自動生成されるファイルがあった覚えがあるけど最近のだと見当たらなかった

# 設定変更

- systemctl edit Unit名
  - /etc/systemd/system/Unit名.d/override.conf を編集
  - edit サブコマンドを使わずに管理しやすい名前のファイル名にしても良い
- systemctl daemon-reload
  - systemctl restart Unit名の前に設定ファイルの変更を反映させる必要あり
  - 忘れていてもメッセージが出るので気づける

# systemctl cat

```console
$ systemctl cat php7.0-fpm.service
# /lib/systemd/system/php7.0-fpm.service
[Unit]
Description=The PHP 7.0 FastCGI Process Manager
After=network.target

[Service]
Type=notify
PIDFile=/run/php/php7.0-fpm.pid
ExecStartPre=/usr/lib/php/php7.0-fpm-checkconf
ExecStart=/usr/sbin/php-fpm7.0 --nodaemonize --fpm-config /etc/php/7.0/fpm/php-fpm.conf
ExecReload=/usr/lib/php/php7.0-fpm-checkconf
ExecReload=/bin/kill -USR2 $MAINPID

[Install]
WantedBy=multi-user.target

# /etc/systemd/system/php7.0-fpm.service.d/override.conf
[Unit]
After=nslcd.service
```

- 上書きも含めて設定確認例
  - LDAP のユーザーを使って権限分離する設定をしている都合で nslcd を待ってから起動する必要があった
