# GlusterFS Server and Heketi Server on Vagrant

Kubernetes - Heketi - GlusterFS テスト用 Vagrantfile


## GlusterFS と Heketi サーバーの開始

次の方法で、３つのGlusterFSサーバー と一つのHeketiサーバーが起動します。

```console
$ vagrant up
```


## クリーンナップ

全て削除するときは、次のコマンドを実行

```console
$ vagrant destroy -f
```
