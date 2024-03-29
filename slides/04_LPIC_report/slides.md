### LPICの概要と受験体験記

---

### まず

- 1と2を合格しました

---

### アンケート

- ご存知の方
- 持っている方

---


### LPICとは

- Linux Professional Institute Certification
- LPIのLinux技術者のための認定プログラム
- NPOで運営されていて, ベンダの依存がない
- ディストリビューションの依存がない

---

### 認定項目

本日はLPIC-1 / LPIC-2 の話

- （Linux Essentials）
- LPIC-1 101/102
- LPIC-2 201/202
- LPIC-3 300(混在環境)
- LPIC-3 303(セキュリティ)
- LPIC-3 304(仮想化とハイアベイラビリティ)

---

### 試験概要

- LPIC-1, 2 共に二つの試験を合格すると認定
- 認定期間は5年
- 試験時間 90分, 60問
- 800点満点(200~), 7割弱で合格
- テストセンターで受験
- 選択式(複数有)、記述式

---

### LPIC-1 試験項目

- 101
  - システムアーキテクチャ (ブートシーケンス、grub, SysV init, systemd)
  - Linuxのインストールとパッケージ管理 (RPM/yum, Debian/deb)
  - GNUおよびUnixコマンド
  - デバイス、Linuxファイルシステム、ファイルシステム階層標準

>>>

- 102
  - シェルとシェルスクリプト (.bashrc)
  - インタフェースとデスクトップ (X11)
  - 管理タスク (useradd, /etc/passwd)
  - 必須システムサービス(cron, ntp, syslog.conf, プリンタ, i18n)
  - ネットワーキングの基礎 (ifconfig, route, DNS, メール)
  - セキュリティ(su, xinetd, ssh)

---

### LPIC-2 試験項目

- 201
  - キャパシティプランニング (vmstat, top, lsof)
  - Linuxカーネル (make install, patch)
  - システム起動 (grub, initramfs, /etc/initab)
  - ファイルシステムとデバイス(mkfs, /etc/fstab)
  - 高度なストレージデバイスの管理 (RAID, LVM, udev)
  - ネットワーク構成 (routing)
  - システム・メンテナンス (makefile, rsync, /etc/issue)

>>>

- 202
  - ドメインネームサーバー (BIND)
  - ウェブサービス (Apache, Nginx)
  - ファイル共有 (Samba, NFS)
  - ネットワーククライアント管理 (DHCP, LDAP, PAM)
  - 電子メールサービス (postfix, dovecot)
  - システムセキュリティ (カーネルパラメータ、iptables, VPN)

---

### つまずきポイント

- 複数選べと出題されるため、解決に複数の方法を知っておく必要がある
- コマンドのオプションは暗記する必要がある
- 問題文の日本語訳に誤字や不正確な文がある
- パッケージとその管理コマンドは、メジャーのものが出題される
- 再受験は難易度があがるため、一回で受かるほうがいい

