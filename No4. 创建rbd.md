
#### 创建pool
```
ceph osd pool create k8sfile 128

设置为128个pgnum

```
#### 设置存储池配额（单位：M）
```
ceph osd pool set-quota k8sfile max_objects 10000
设置最大对象10000个
```

#### 创建rbd 

RBD块Ceph支持两种格式：

format 1:新建RBD镜像时使用最初的格式，此格式兼容所有版本的librbd和内核模块，但是不支持较新的功能，像克隆。

format 2:使用第二版rbd格式，librbd和3.11版以上内核模块才支持（除非是拆分的模块）。此格式增加了克隆支持，使得扩展更容易，还允许以后增加新功能。

```
rbd create k8sfile/docker --size 4096 --image-feature layering
20480：大小单位M
docker：rbd名字
k8sfile:存储池
```
#### 3.1 以上内核用下面的操作
```
rbd create k8sfile/docker --size 4096 --image-format 2  --image-feature layering
```
#### 映射到块设备
```
rbd map k8sfile/docker --name client.admin
```