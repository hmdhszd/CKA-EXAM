## Install ectdctl on Ubuntu:

```bash
sudo apt update
sudo apt install vim wget curl

export RELEASE=$(curl -s https://api.github.com/repos/etcd-io/etcd/releases/latest|grep tag_name | cut -d '"' -f 4)
wget https://github.com/etcd-io/etcd/releases/download/${RELEASE}/etcd-${RELEASE}-linux-amd64.tar.gz


tar xvf etcd-${RELEASE}-linux-amd64.tar.gz
cd etcd-${RELEASE}-linux-amd64
sudo mv etcd etcdctl etcdutl /usr/local/bin 
```

Confirm version:
```bash
$ etcd --version
etcd Version: 3.5.2
Git SHA: 99018a77b
Go Version: go1.16.3
Go OS/Arch: linux/amd64

$ etcdctl version
etcdctl version: 3.5.2
API version: 3.5

$ etcdutl version
etcdutl version: 3.5.2
API version: 3.5
```

## Take a snapshot of ETCD

```bash
root@master:~# ETCDCTL_API=3 etcdctl \
--endpoints https://127.0.0.1:2379 \
--cert /etc/kubernetes/pki/etcd/server.crt \
--cacert /etc/kubernetes/pki/etcd/ca.crt \
--key /etc/kubernetes/pki/etcd/server.key snapshot save /tmp/snapshot-pre-boot.db

Snapshot saved at /tmp/snapshot-pre-boot.db

```

Verify the snapshot:

```bash
root@master:~# ETCDCTL_API=3 etcdctl --write-out=table snapshot status /tmp/snapshot-pre-boot.db

+----------+----------+------------+------------+
|   HASH   | REVISION | TOTAL KEYS | TOTAL SIZE |
+----------+----------+------------+------------+
| 84d884bd |   153327 |       1667 |     4.8 MB |
+----------+----------+------------+------------+
```


## Restore the snapshot of ETCD

```bash
root@master:~# ETCDCTL_API=3 etcdctl \
--endpoints https://127.0.0.1:2379 \
--cert /etc/kubernetes/pki/etcd/server.crt \
--cacert /etc/kubernetes/pki/etcd/ca.crt \
--key /etc/kubernetes/pki/etcd/server.key \
--name=master \
--initial-cluster-token etcd-cluster-1 \
--initial-advertise-peer-urls=https://127.0.0.1:2380 \
snapshot restore /tmp/snapshot-pre-boot.db
```
