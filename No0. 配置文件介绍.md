```
[global]  #全局，要将配置设置应用于整个集群，请在[global]下输入配置设置。
fsid = 9ec20e59-1033-4505-920d-113183167b31     #CLUSTER ID，每个Ceph存储集群都有唯一的标识符（fsid）。
mon initial members = ceph-153,ceph-154,ceph-155  #初始成员，建议最少运行三个ceph监视器在生产存储集群，以确保高可用性。启动期间集群中初始监视器的ID。 如果指定，Ceph需要奇数个监视器来形成初始定额
mon host = 192.168.1.153,192.168.1.154,192.168.1.155  #这里是主机名并非主机IP，当然如果是主机IP的形式，可以省略下面的[mon]
auth_cluster_required = cephx    #如果启用了，集群守护进程（如 ceph-mon 、 ceph-osd 和 ceph-mds ）间必须相互认证。可用选项有 cephx 或 none 。默认是cephx启用。
auth_service_required = cephx    #如果启用，客户端要访问 Ceph 服务的话，集群守护进程会要求它和集群认证。可用选项为 cephx 或 none 。类型:String是否必需:No默认值:cephx.
auth_client_required = cephx     #如果启用，客户端会要求 Ceph 集群和它认证。可用选项为 cephx 或 none 。 默认就是cephx。   
public_network = 192.168.1.0/24  #设置在[global]。可以指定以逗号分隔的子网。这是分配集群的外网网段，即对外数据交流的网段。
osd journal size = 1024    #缺省值为0。你应该使用这个参数来设置日志大小。日志大小应该至少是预期磁盘速度和filestore最大同步时间间隔的两倍。如果使用了SSD日志，最好创建大于10GB的日志，并调大filestore的最小、最大同步时间间隔。 
osd pool default size = 2   #osd的默认副本数，如果不设置默认是3份。
osd pool default min size = 1  #缺省值是0.这是处于degraded状态的副本数目，它应该小于osd pool default size的值，为存储池中的object设置最小副本数目来确认写操作。即使集群处于degraded状态。如果最小值不匹配，Ceph将不会确认写操作给客户端。 
osd pool default pg num = 333   #每个存储池默认的pg数
osd pool default pgp num = 333  #PG和PGP的个数应该保持一致。PG和PGP的值很大程度上取决于集群大小。
osd crush chooseleaf type = 1   #CRUSH规则用到chooseleaf时的bucket的类型，默认值就是1.
[osd]
osd_journal_size = 5120
[mon]
mon osd allow primary affinity = true
```