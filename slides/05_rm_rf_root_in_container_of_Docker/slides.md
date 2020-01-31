### Docker コンテナの中で rm -rf / した

---

### rm -rf /

- rm: remove files or directories
- r : remove directories and their contents recursively
- f : ignore nonexistent files and arguments, never prompt
- / : ファイルシステム上の最上位のディレクトリ

---

### Docker とは

以下を合わせたプロダクトのこと

- コンテナ型仮想化技術を実現する常駐ソフトウェア
- それを操作するコマンドフロントインターフェース

---

### コンテナ型仮想化技術

- namespaceを用いて、プロセスやファイルシステムを分離する
- コンテナ上のデータを消しても, ホスト上のデータに影響はない

---

本発表では、コンテナ上のデータを全て削除するとどうなったかを発表する。

---

### 環境の説明の前に...

- ホストOS: MacOS
  - コンテナの技術は Linux の技術なので、本来はできない
  - Docker for Mac (HyperKit) で代用する
  - 今回はfileSystem(データ)の話なので、実験に問題はない

---

### 環境

- ホストOS: MacOS
- コンテナ側のOS: Debian 10 (Buster)
  
  - 選定理由は特にありません

---

### 作業ディレクトリ

```
xxx:~ $ ls
Dockerfile	rootfs.tar.xz
```

---

### DockerFile

- https://github.com/debuerreotype/docker-debian-artifacts/tree/7a782c4f5f3a161936db0cd26a2623b4017d797c/buster

```
FROM scratch
ADD rootfs.tar.xz /
CMD ["bash"]
```

---

### イメージの作成

```
xxx:~$ docker image build -t example/rmrf:latest .
Sending build context to Docker daemon  30.12MB
Step 1/3 : FROM scratch
 --->
Step 2/3 : ADD rootfs.tar.xz /
 ---> fa260dd365f1
Step 3/3 : CMD ["bash"]
 ---> Running in b4b65f70db91
Removing intermediate container b4b65f70db91
 ---> b3c29a66b431
Successfully built b3c29a66b431
Successfully tagged example/rmrf:latest
```

---

### コンテナを作成し、実行中に

- そのまま実行すると、コンテナが停止するため -it でアクセス

```
xxx:~$ docker container run -it --name rmrf example/rmrf:latest
root@885f49219da2:/#
```

---

### コンテナの中身をのぞく

```
root@7402d462a175:/# ls
bin  boot  dev	etc  home  lib	lib64  media  mnt  opt	proc  root  run  sbin  srv  sys  tmp  usr  var
```

---

### ではさっそく...

```
root@7402d462a175:/# rm -rf /
rm: it is dangerous to operate recursively on '/'
rm: use --no-preserve-root to override this failsafe
```

---

### 気をとりなおして...

```
root@8fcdeeaa7810:/# rm -rf / --no-preserve-root
rm: cannot remove '/sys/kernel/fscache': Read-only file system
rm: cannot remove '/sys/kernel/uevent_helper': Read-only file system
rm: cannot remove '/sys/kernel/profiling': Read-only file system

略 (12000行くらい)

rm: cannot remove '/dev/pts/ptmx': Operation not permitted
rm: cannot remove '/etc/hostname': Device or resource busy
rm: cannot remove '/etc/resolv.conf': Device or resource busy
rm: cannot remove '/etc/hosts': Device or resource bust
root@8fcdeeaa7810:/#
```

---

### 生きている人いますか

```
root@8fcdeeaa7810:/# ls
bash: ls: command not found
```

---

### bash のパス補完を使う

```
root@8fcdeeaa7810:/# /
dev/  etc/  proc/ sys/
```

---

### 余談: 誰得テクニック

- bashの一つ目の補完は実行ファイルを想定
- なので、実行ファイル属性のファイルしか補完できない
- なので、パラメータの部分で補完を行う

```
root@8fcdeeaa7810:/# hoge /etc/
hostname     hosts        resolv.conf
```

---

消せなかったファイルの消せない理由を考察する

---

### /proc 以下のファイル

- カーネルやプロセスが保持する情報を保持
- ファイル自体は存在しない

>>>

### 確かに消せなさそうだが...

- どの処理で吐かれたエラー?

```
rm: cannot remove '/proc/1/task/1/fd/0': Operation not permitted
```

- ファイルの属性から読み取れなかった

```
root@cabf7e393d9f:/proc# ls -al /proc/cpuinfo
-r--r--r-- 1 root root 0 Jan 30 22:24 /proc/cpuinfo
```

---

### /sys 以下のファイル

- /proc 以下がごちゃごちゃしすぎたために生まれたディレクトリ (2.6)
- デバイスやドライバの情報を取得できる
- /proc と同様ファイル自体は存在しない

>>>

#### ファイルシステムが read-only であるため

```
rm: cannot remove '/sys/kernel/fscache': Read-only file system
rm: cannot remove '/sys/devices/pci0000:00/PNP0103:00/power/autosuspend_delay_ms': Read-only file system
```

```
root@cabf7e393d9f:/proc# mount | grep 'sysfs'
sysfs on /sys type sysfs (ro,nosuid,nodev,noexec,relatime)
```

---

### /dev 以下のファイル

- 接続されたデバイスを表すファイルが置かれる
- /proc と同様ファイル自体は存在しない

>>>

### まったくわからん

```
rm: cannot remove '/dev/console': Device or resource busy
rm: cannot remove '/dev/shm': Device or resource busy
rm: cannot remove '/dev/mqueue': Device or resource busy
```

- 別のプロセスで該当ファイルが使用されてはいない
- しかし、rm 実行中には使用されている可能性がある

```
root@cabf7e393d9f:/dev# find /proc/*/ -type l -printf '%p => %l\n'  2>/dev/null | grep -E '/proc/[0-9]+' | grep '/dev/console'
root@cabf7e393d9f:/dev# find /proc/*/ -type l -printf '%p => %l\n'  2>/dev/null | grep -E '/proc/[0-9]+' | grep '/dev/shm'
root@cabf7e393d9f:/dev# find /proc/*/ -type l -printf '%p => %l\n'  2>/dev/null | grep -E '/proc/[0-9]+' | grep '/dev/mqueue'
```

>>>

- Sticky Bit 付きだが、 root権限で実行している

```
root@cabf7e393d9f:/dev# ls -al
total 8
...
drwxrwxrwt 2 root root     40 Jan 30 22:00 mqueue
drwxrwxrwt 2 root root     40 Jan 30 22:00 shm
...
```

- 原因と違うエラーが吐かれている可能性

---

### /etc 以下のファイル

- システム管理用の設定ファイルや、各種ソフトウェアの設定ファイルが置かれる

>>>

####  本来ただの設定ファイルが消せないのはおかしい

```
rm: cannot remove '/etc/hostname': Device or resource busy
rm: cannot remove '/etc/resolv.conf': Device or resource busy
rm: cannot remove '/etc/hosts': Device or resource busy
```

>>>

### findmnt をみると面白い結果が出た

```
|-/etc/resolv.conf /dev/sda1[/docker/containers/cabf7e393d9f18a7395e2380e8e6502eb948c6adcd186c882c969498131708f2/resolv.conf]
|                                                                               ext4    rw,relatime,stripe=1024,data=ordered
|-/etc/hostname    /dev/sda1[/docker/containers/cabf7e393d9f18a7395e2380e8e6502eb948c6adcd186c882c969498131708f2/hostname]
|                                                                               ext4    rw,relatime,stripe=1024,data=ordered
`-/etc/hosts       /dev/sda1[/docker/containers/cabf7e393d9f18a7395e2380e8e6502eb948c6adcd186c882c969498131708f2/hosts]
                                                                                ext4    rw,relatime,stripe=1024,data=ordered
```

>>>

### 原因はDocker Engine にあるみたい 


- コンテナの作成時, ホストのディレクトリをコンテナ側にマウントできる
- /etc 以下でその機能を使うと hosts, hostname が上書きされてしまい, 最悪コンテナと疎通ができなくなる
- あくまで 以下の stackoverflow の言い分

https://stackoverflow.com/questions/56578998/what-is-the-meaning-of-mounting-dev-sda1-to-etc-hosts-in-docker-container

