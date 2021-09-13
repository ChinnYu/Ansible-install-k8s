# Kubernetes v1.20 企業級高可用集群自動部署（離線版）
>### 注：確保所有節點系統時間一致
>### 作業系統要求：CentOS7.x_x64

### 1、找一台伺服器安裝Ansible
```
# yum install epel-release -y
# yum install ansible -y
```
### 2、下載所需文件

下載Ansible部署文件：


```
# tar zxf binary_pkg.tar.gz
```
### 3、修改Ansible檔

修改hosts檔，根據規劃修改對應IP和名稱。

```
# vi hosts
...
```
修改group_vars/all.yml檔，修改套裝軟體目錄和證書可信任IP。

```
# vim group_vars/all.yml
software_dir: '/root/binary_pkg'
...
cert_hosts:
  k8s:
  etcd:
```
## 4、一鍵部署
### 4.1 架構圖
單Master架構
![avatar](https://github.com/lizhenliang/ansible-install-k8s/blob/master/single-master.jpg)

多Master架構
![avatar](https://github.com/lizhenliang/ansible-install-k8s/blob/master/multi-master.jpg)
### 4.2 部署命令
單Master版：
```
# ansible-playbook -i hosts single-master-deploy.yml -uroot -k
```
多Master版：
```
# ansible-playbook -i hosts multi-master-deploy.yml -uroot -k
```

## 5、查看集群節點
```
# kubectl get node
NAME          STATUS   ROLES    AGE   VERSION
k8s-master    Ready    <none>   9h    v1.20.4
k8s-node1     Ready    <none>   9h    v1.20.4
k8s-node2     Ready    <none>   9h    v1.20.4
```

## 6、其他
### 6.1 部署控制
如果安裝某個階段失敗，可針對性測試.

例如：只運行部署外掛程式
```
# ansible-playbook -i hosts single-master-deploy.yml -uroot -k --tags addons
```

### 6.2 節點擴容
1）修改hosts，添加新節點ip
```
# vi hosts
...
[newnode]
192.168.31.75 node_name=k8s-node3
```
2）執行部署
```
# ansible-playbook -i hosts add-node.yml -uroot -k
```
### 6.3 所有HTTPS證書存放路徑
部署產生的證書都會存放到目錄“ansible-install-k8s-master/ssl”，一定要保存好，後面還會用到~

<br/>
<br/>