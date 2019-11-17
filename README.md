# GlusterFS Server and Heketi Server on Vagrant

この Vagrant と Ansibl/e のコードは、下記の４つの仮想サーバーに　[GlusterFS](https://www.gluster.org/) と [Heketi](https://github.com/heketi/heketi)を構築して、Kuberentes クラスタのポッドからマウントして利用できるようにするものです。この仮想サーバーのIPアドレスは、仮想サーバーに割り当てられる内部通信用のIPアドレスです。

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
$ cd vagrant-glusterfs
$ vagrant up
```

## Kubernetes の ポッドからの利用

[https://github.com/takara9/vagrant-kubernetes](https://github.com/takara9/vagrant-kubernetes)で構築したクラスタからテストするには、k8s-yaml マニフェストを適用します。

Kubernetesクラスタのマスターノードにログインして、このリポジトリをクローンして、以下のディレクトに移動します。

```
$ vagrant ssh master
$ git clone https://github.com/takara9/vagrant-glusterfs
$ cd vagrant-glusterfs/k8s-yaml
```

このディレクトリには、Heketiと連携するプロビジョナーを定義したストレージクラスがあります。
そしてストレージをダイアミック・プロビジョニングするサンプルのマニフェストがありますから、
GlusterFSをマウントするポッドを起動して確認することができます。

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

## デフォルトストレージにする

ストレージクラスの設定を省略した場合、GlasterFSをデフォルトストレージに設定することができます。
それには、その時点の設定に、パッチを当てて、部分的に設置を変更します。

```
$ kubectl patch storageclass gluster-heketi -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

上記を設定することで、以下のようになります。

```
$ kubectl get storageclass
NAME                       PROVISIONER               AGE
gluster-heketi (default)   kubernetes.io/glusterfs   35m
```



## 一時停止と起動

`vagrant halt` でクラスタの仮想サーバーをシャットダウン、`vagrant up` で再び起動します。


## クリーンナップ

全て削除するときは、次のコマンドを実行する。データも失われるので実行するときは注意ねがいます。

```
$ vagrant destroy -f
```

## 障害対応

このGlusterFSでは、ルートユーザー以外で動作するコンテナが、ファイルシステムをマウントして、書き込みする場合、`Permission denined` のエラーで異常終了する。これは、マウントポイントのファイルシステムが、rootユーザーに設定されているためである。

* Bug 1312421 - glusterfs mount-point return permission denied, https://bugzilla.redhat.com/show_bug.cgi?id=1312421
* POSIX Access Control Lists, https://docs.gluster.org/en/latest/Administrator%20Guide/Access%20Control%20Lists/
* Product Documentation for Red Hat Gluster Storage 3.5, https://access.redhat.com/documentation/ja-jp/red_hat_gluster_storage/3.5/
* not able to configure with non root user #314, https://github.com/gluster/glusterfs/issues/314

Red HatのGlusteFSでは、OpenShift v3から問題が解決されている様であるが、コミュニテイ版では、この問題に手作業で対応する必要がある。

対応方法は、以下の順番で、k8s-yaml/chmod-pod.yml を適用して、非ルートユーザーにも書き込み権限を与える。

```
$ kubectl get pvc 　 PVC名取得
$ vi chmod-pod.yml   PVC名をセット
$ kubectl apply -f chmod-pod.yml 
```

マニフェストの中の変更箇所は、最後の行の change-me 部分である。

```file:chmod-pod.yml
apiVersion: v1
kind: Pod
metadata:
  name: gfs-chmode
spec:
  containers:
  - name: change-mode
    image: alpine 
    volumeMounts:
    - name: gfs
      mountPath: /mnt
    command: [ "chmod", "a+w", "/mnt"]
  volumes:
  - name: gfs
    persistentVolumeClaim:
      claimName: <change-me>  <- このPVCの名前で置き換える
```
