## hosts文件
```
172.27.0.9 k8-1
172.27.0.11 k8-2
172.27.0.12 k8-3

```

## ceph.conf
```
[global]
fsid = bd60349e-c967-4907-997e-98525af74ac5
mon initial members = k8-1,k8-2,k8-3
auth_cluster_required = cephx
auth_service_required = cephx
auth_client_required = cephx
public_network = 172.27.0.0/20

[osd]
osd_journal_size = 10240
osd max object name len = 256
osd max object namespace len = 64


[mon]
mon osd allow primary affinity = true
mon host = k8-1,k8-2,k8-3

[mon.k8-1]
host = k8-1
mon addr = 172.27.0.9:6789

[mon.k8-2]
host = k8-2
mon addr = 172.27.0.11:6789

[mon.k8-3]
host = k8-3
mon addr = 172.27.0.12:6789

```

#### 为群集创建密钥环，并生成监视密钥
```
ceph-authtool --create-keyring /tmp/ceph.mon.keyring --gen-key -n mon. --cap mon 'allow *'
```

#### 生成一个管理员密钥环，生成一个client.admin用户，并将该用户添加到密钥环中
```
ceph-authtool --create-keyring /etc/ceph/ceph.client.admin.keyring --gen-key -n client.admin --set-uid=0 --cap mon 'allow *' --cap osd 'allow *' --cap mds 'allow *' --cap mgr 'allow *' 
```
#### 将client.admin密钥添加到ceph.mon.keyring
```
ceph-authtool /tmp/ceph.mon.keyring --import-keyring /etc/ceph/ceph.client.admin.keyring
```

#### 创建目录，这里有点绕第一个ceph是集群的名称，第一个ceph集群默认的集群名称就是ceph。后面的k8-1是主机名
```
mkdir /var/lib/ceph/mon/k8-1
```

#### 使用主机名，主机IP地址和FSID生成监视器映射。保存为/tmp/monmap
```
monmaptool --create --add k8-1 172.27.0.9 --add k8-2 172.27.0.11 --add k8-3 172.27.0.12  --fsid  bd60349e-c967-4907-997e-98525af74ac5 /tmp/monmap
```

#### 授权
```
chown -R ceph:ceph /var/lib/ceph/
chown  ceph:ceph /tmp/monmap
chown  ceph:ceph /tmp/ceph.mon.keyring
sudo -u ceph ceph-mon --mkfs -i k8-1  --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring
```

#### 复制文件到其他mon节点
```
scp /etc/ceph/* k8-2:/etc/ceph/
scp /etc/ceph/* k8-3:/etc/ceph/
scp /tmp/ceph.mon.keyring k8-2:/tmp/
scp /tmp/ceph.mon.keyring k8-3:/tmp/
```
## 登录到第二个mon节点
```
monmaptool --create --add k8-2 172.27.0.11 --fsid bd60349e-c967-4907-997e-98525af74ac5  /tmp/monmap
mkdir /var/lib/ceph/mon/k8-2
chown -R ceph:ceph /var/lib/ceph/
chown  ceph:ceph /tmp/monmap
chown  ceph:ceph /tmp/ceph.mon.keyring
sudo -u ceph ceph-mon --mkfs -i k8-2 --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring
```
## 登录到第三个mon节点
```
monmaptool --create --add k8-3 172.27.0.12 --fsid bd60349e-c967-4907-997e-98525af74ac5  /tmp/monmap
chown -R ceph:ceph /var/lib/ceph/
chown  ceph:ceph /tmp/monmap
chown  ceph:ceph /tmp/ceph.mon.keyring
sudo -u ceph ceph-mon --mkfs -i k8-3 --monmap /tmp/monmap --keyring /tmp/ceph.mon.keyring
 ```


#### 查看端口状态
```
lsof -i:6789
```
#### 查看集群状态
```
ceph -s
```
