# Open Genera 2.0 をインストールする

- 環境
	- インストールしたてのUbuntu20.04 LTS on VirtualBox
		- UID:1000,GID:1000 の(インストール時に作成するデフォルトの)ユーザーです
- ドキュメント
	- https://archives.loomcom.com/genera/genera-install.html
		2020-11-02 の記事

# インストール

```
$ sudo apt install curl
$ mkdir ~/genera
$ cd genera
$ curl -L -O https://archives.loomcom.com/genera/genera
$ chmod a+x
$ curl -L -O https://archives.loomcom.com/genera/worlds/VLM_debugger
$ curl -L -O https://archives.loomcom.com/genera/worlds/dot.VLM
$ cd /var/lib/
$ sudo curl -L -O https://archives.loomcom.com/genera/var_lib_symbolics.tar.gz
$ sudo tar xvf var_lib_symbolics.tar.gz
$ sudo chown -R $USER:$USER symbolics
$ cd ~/genera/
$ vi .VLM 
genera.worldSearchPath: /home/seth/genera
↓変更
genera.worldSearchPath: ~/genera

$ sudo vi /etc/hosts	# 以下２行を追加
192.168.2.1    genera-vlm
192.168.2.2    genera/

$ sudo apt install inetutils-inetd
$ sudo vi /etc/inetd.conf # 以下４行を追加
time      stream  tcp  nowait root internal
time      dgram   udp  wait   root internal
daytime   stream  tcp  nowait root internal
daytime   dgram   udp  wait   root internal

$ sudo systemctl restart inetutils-inetd.service
$ telnet localhost 13	# このコマンドを打って次の表示になることを確認, 13番ポート
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Sun Dec 19 16:01:36 2021
Connection closed by foreign host.

$ telnet localhost 37	# 同様に37番ポート
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
Connection closed by foreign host.

$ sudo apt install nfs-common nfs-kernel-server
$ sudo vi /etc/exports	# 最下行に１行追加
/       genera(rw,sync,no_subtree_check,all_squash,anonuid=1000,anongid=1000)

$ sudo vi /etc/default/nfs-kernel-server # 以下の２つの行見つけて値を変更
RPCNFSDCOUNT="--nfs-version 2 8"
RPCMOUNTDOPTS="--nfs-version 2 --manage-gids"

$ sudo systemctl restart nfs-kernel-server
$ sudo exportfs -rav	# 次のように表示されることを確認
exporting genera:/

$ sudo ip tuntap add dev tap0 mode tap
$ sudo ip addr add 192.168.2.1/24 dev tap0
$ sudo ip link set dev tap0 up

$ ping 192.168.2.1		# ping が通ることを確認
PING 192.168.2.1 (192.168.2.1) 56(84) バイトのデータ
64 バイト応答 送信元 192.168.2.1: icmp_seq=1 ttl=64 時間=0.021ミリ秒
64 バイト応答 送信元 192.168.2.1: icmp_seq=2 ttl=64 時間=0.052ミリ秒
^C

$ ./genera				# Open Genera 2.0 が起動しました！
```

# Genera の止め方

halt genera コマンドでシャットダウンできます。

```
Command: halt genera
```
この後3回に渡って止めていいか聞かれますが、y で答えてください。


# Ubuntu 再起動後のOpen Genera 2.0 の起動

```
$ # この部分は毎回実行する必要があります。
$ sudo ip tuntap add dev tap0 mode tap
$ sudo ip addr add 192.168.2.1/24 dev tap0
$ sudo ip link set dev tap0 up
$ # そののち、./genela で起動します
$ cd ~/genera
$ ./genera				# Open Genera 2.0 が起動しました
```

私はこの部分をシェルスクリプトにしましたが、TUN/TAP 周りについては毎回設定しなくてすむ方法があるはずです。

# ドキュメントの残りの部分

ここから先はこちらに続きがあります。
https://archives.loomcom.com/genera/genera-install.html#org3b9dffc

私はこの 3.12. Save the World
https://archives.loomcom.com/genera/genera-install.html#orgc648f53
でイメージファイルを作ったあと、そのイメージで起動すると*真っ白い画面のまま* になってしまう現象に出会い、解決しておりません。


