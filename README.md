# GlusterFS Server and Heketi Server on Vagrant

この Vagrant と Ansible のコードは、下記の４つの仮想サーバーに　[GlusterFS](https://www.gluster.org/) と [Heketi](https://github.com/heketi/heketi)を構築して、Kuberentes クラスタのポッドからマウントして利用できるようにするものです。


1. heketi   172.20.1.20  
1. gluster1 172.20.1.21　
1. gluster1 172.20.1.22
1. gluster1 172.20.1.23

[https://github.com/takara9/vagrant-kubernetes](https://github.com/takara9/vagrant-kubernetes)で構築するKubernetesクラスタと組み合わせて、永続ストレージのオートプロビジョニング環境のミニチュア版を構築できます。

## このクラスタを起動するために必要なソフトウェア

このコードを利用するためには、次のソフトウェアをインストールしていなければなりません。

* Vagrant (https://www.vagrantup.com/)
* VirtualBox (https://www.virtualbox.org/)
* kubectl (https://kubernetes.io/docs/tasks/tools/install-kubectl/)
* git (https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## 仮想マシンのホスト環境

Vagrant と VirtualBox が動作するOSが必要です。

* Windows10　
* MacOS
* Linux

推奨ハードウェアと言えるか、このコードの筆者の環境は以下のとおりです。

* RAM: 8GB 以上
* ストレージ: 空き領域 5GB 以上
* CPU: Intel Core i5 以上


## GlusterFS と Heketi サーバーの開始

次のコマンドで、３つの GlusterFS と 一つの Heketi ノードが起動します。

```
$ git clone https://github.com/takara9/vagrant-glusterfs
$ vagrant up
```

## Kubernetes の ポッドからの利用

[https://github.com/takara9/vagrant-kubernetes](https://github.com/takara9/vagrant-kubernetes)で構築したクラスタからテストするには、k8s-yaml マニフェストを適用します。

```
kubectl apply -f storageclass.yml (ストレージクラスにHeketiのエンドポイントを登録)
kubectl apply -f gfs-pvc-1.yml (Persistent Volume Claim で PV をプロビジョニング)
kubectl apply -f gfs-client.yml (PVCをマウントするポッドを２つ起動)
```

PVCとPVの生成を確認するには、次のようにします。(Windows10の例)

```
C:\Users\Maho\vagrant-glusterfs\k8s-yaml>kubectl get pvc
NAME      STATUS    VOLUME                                     CAPACITY   ACCESS MODES   STORAGECLASS     AGE
gvol-1    Bound     pvc-d8538128-2a07-11e9-b4c1-02f6928eb0b4   10Gi       RWX            gluster-heketi   4h

C:\Users\Maho\vagrant-glusterfs\k8s-yaml>kubectl get pv
NAME                                       CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS    CLAIM            STORAGECLASS     REASON    AGE
pvc-d8538128-2a07-11e9-b4c1-02f6928eb0b4   10Gi       RWX            Delete           Bound     default/gvol-1   gluster-heketi             4h
```

次はポッドからのマウントを確認例です。

```
C:\Users\Maho\vagrant-glusterfs\k8s-yaml>kubectl get po
NAME                          READY     STATUS    RESTARTS   AGE
gfs-client-5f8569b685-ljd5c   1/1       Running   0          4h
gfs-client-5f8569b685-vr7r5   1/1       Running   0          4h

C:\Users\Maho\vagrant-glusterfs\k8s-yaml>kubectl exec -it gfs-client-5f8569b685-ljd5c sh
# df -h
Filesystem                                        Size  Used Avail Use% Mounted on
overlay                                           9.7G  2.0G  7.7G  21% /
tmpfs                                              64M     0   64M   0% /dev
tmpfs                                             497M     0  497M   0% /sys/fs/cgroup
172.20.1.21:vol_05274b489f63bf6341f9bc3449d8c2ce   10G   66M   10G   1% /mnt
/dev/sda1                                         9.7G  2.0G  7.7G  21% /etc/hosts
shm                                                64M     0   64M   0% /dev/shm
tmpfs                                             497M   12K  497M   1% /run/secrets/kubernetes.io/serviceaccount
tmpfs                                             497M     0  497M   0% /proc/scsi
tmpfs                                             497M     0  497M   0% /sys/firmware
```


## 一時停止と起動

vagrant halt でクラスタの仮想サーバーをシャットダウン、vagrant up で再び起動します。


## クリーンナップ

全て削除するときは、次のコマンドを実行する。データも失われるので実行するときは注意ねがいます。

```
$ vagrant destroy -f
```
