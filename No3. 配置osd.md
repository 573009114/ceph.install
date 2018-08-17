## 创建osd集群 
#### 创建新的OSD（三台机器上面都执行）
```
ceph-disk prepare --cluster ceph --cluster-uuid  bd60349e-c967-4907-997e-98525af74ac5 --fs-type ext4 /data/


```


#### 分别在机器上面创建ceph.keyring
```
ceph-create-keys --id k8-1
```
#### 目录授权
```
chown -R ceph:ceph
```

#### 激活osd 每台执行
```
ceph-disk activate /data/
```

#### 查看ceph osd进程

```
ps -ef|grep ceph
```

```
ceph -s
```
#### 正常状态如下：
```
    cluster bd60349e-c967-4907-997e-98525af74ac5
     health HEALTH_OK
     monmap e1: 3 mons at {k8-1=172.27.0.9:6789/0,k8-2=172.27.0.11:6789/0,k8-3=172.27.0.12:6789/0}
            election epoch 10, quorum 0,1,2 k8-1,k8-2,k8-3
     osdmap e23: 3 osds: 3 up, 3 in
            flags sortbitwise,require_jewel_osds
      pgmap v749: 64 pgs, 1 pools, 0 bytes data, 0 objects
            5849 MB used, 134 GB / 147 GB avail
                  64 active+clean

```