# GlusterFS Server and Heketi Server on Vagrant

この Vagrant と Ansible のコードは、下記の４つの仮想サーバーに　GlusterFS と Heketi
を構築して、Kuberentes クラスタのポッドからマウントして利用できるようにするものです。


1. heketi   172.20.1.20  https://github.com/heketi/heketi
1. gluster1 172.20.1.21　https://www.gluster.org/
1. gluster1 172.20.1.22
1. gluster1 172.20.1.23


[https://github.com/takara9/vagrant-kubernetes](https://github.com/takara9/vagrant-kubernetes)で構築するKubernetesクラスタと組み合わせて、
永続ストレージのオートプロビジョニング環境のミニチュア版を構築できます。


vagrant up を
実行することで、次の４つのサーバーが起動して、Kubernetes のポッドから GlasterFSを
利用できる様になります。





## GlusterFS と Heketi サーバーの開始

次の方法で、３つのGlusterFSサーバー と一つのHeketiサーバーが起動します。

```console
$ git clone https://github.com/takara9/vagrant-glusterfs
$ vagrant up
```


## クリーンナップ

全て削除するときは、次のコマンドを実行

```console
$ vagrant destroy -f
```
