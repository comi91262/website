## LPIC レポート

---

### LPICとは

- Linux Professional Institute Certification
- LPIのLinux技術者のための認定プログラム
- NPOでなので, ベンダの依存がない
- ディストリビューションの依存がない

---

### Linuxとは?

- 省略

---

### 認定項目

- （Linux Essentials）
- LPIC-1 101/102  <- 今回はここの話
- LPIC-2 201/202  <- 今回はここの話
- LPIC-3 300(混在環境)
- LPIC-3 303(セキュリティ)
- LPIC-3 304(仮想化とハイアベイラビリティ)

---

### 試験概要

- LPIC-1, 2 共に二つの試験を合格すると認定
- 期間は5年
- 試験時間 90分, 60問
- 800点満点, 7割弱で合格
- テストセンターで受験
- 選択式、記述式


---

### LPIC-1 試験項目

- 101
  - システムアーキテクチャ (ブートローダ、ランレベルとか)
  - Linuxのインストールとパッケージ管理
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
  - Linuxカーネル (install, patch)
  - システム起動 (grub, initramfs, /etc/initab)
  - ファイルシステムとデバイス(mkfs, /etc/fstab)
  - 高度なストレージデバイスの管理 (RAID, LVM, udev)
  - ネットワーク構成 (routing)
  - システム・メンテナンス (makefile, rsync, /etc/issue)

>>>

- 202
  - ドメインネームサーバー (BIND)
  - ウェブサービス (Apache, Nginx)
  - ファイル共有 (Samba)
  - ネットワーククライアント管理 (DHCP, LDAP)
  - 電子メールサービス (postfix, dovecot)
  - システムセキュリティ (カーネルパラメータ、iptables, VPN)

---

### 進捗

- 11/10 101/102 不合格
- 11/16 201 合格
- 11/23 101 合格
- 11/30 102 再受験予定

---

### つまずきポイント (守秘義務あるのでざっくり)

- 二つ選べ、とか片方を知っていても駄目
- コマンドのオプションは暗記する必要がある
- 問題文の日本語訳が不正確
- パッケージと管理コマンドは、メジャーのものが出題される
    - dkpg, apt, rpm, yum  
