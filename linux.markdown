## Linux - centos

### 常用命令
- 打包: tar -zcvf 目标包名 目标文件夹
- 解压: tar -xzvf 目标包名
- ssh传送: scp crm.tar.gz root@118.24.23.200:/data/kyhs-crm

### 安装图形界面
```
yum groupinstall "X Window System"
yum groupinstall "GNOME Desktop"
systemctl set-default graphical.target
```

### `samba`共享
```
//安装
yum install -y samba
// 修改共享目录权限
sudo chmod 777 需要共享的目录
// 备份配置文件
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf.bak
// 修改配置文件，在末尾添加
// "[share]
// path=/home/share
// available = yes
// browseable = yes
// public = yes
// writable = yes"
sudo vim /etc/samba/smb.conf
// 添加账户
sudo touch /etc/samba/smbpasswd
sudo smbpasswd -a USER_NAME
// 启动服务
service smb start


// 共享文件设置
chcon -t samba_share_t 共享目录
chmod 777 共享目录

// 安装图形界面
//'http://archives.fedoraproject.org/pub/archive/fedora/linux/releases/14/Everything/source/SRPMS/system-config-samba-1.2.90-1.fc14.src.rpm'
//http://archives.fedoraproject.org/pub/archive/fedora/linux/releases/14/Everything/source/SRPMS/system-config-samba-docs-1.0.9-1.fc14.src.rpm
// 安装编译工具
yum install -y rpm-build
// 开始编译
rpmbuild --rebuild system-config-samba-0.99.47-1.fc14.src.rpm 
rpmbuild --rebuild system-config-samba-docs-1.0.9-1.fc14.src.rpm
// 切换到编译好的目录
cd /root/rpmbuild/RPMS/noarch/
// 开始安装
rpm -ivh system-config-samba-1.2.90-1.el7.centos.noarch.rpm system-config-samba-docs-1.0.9-1.el7.centos.noarch.rpm
// 打开界面
system-config-samba
```

