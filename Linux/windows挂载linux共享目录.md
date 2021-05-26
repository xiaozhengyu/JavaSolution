# windows挂载linux共享目录（Ubuntu）

---

## 1. 安装Samba

```shell
sudo apt-get install samba
```

![image-20210526171243022](markdown/windows挂载linux共享目录.assets/image-20210526171243022.png)

## 2. 查看Samba版本，验证安装结果

```shell
samba
```

![image-20210526171405114](markdown/windows挂载linux共享目录.assets/image-20210526171405114.png)

## 3.修改Samba配置文件

### 3.1 备份

```shell
sudo cp /etc/samba/smb.conf  /etc/samba/smb.conf.bak
```

```shell
ls /etc/samba/
```

![image-20210526172435869](markdown/windows挂载linux共享目录.assets/image-20210526172435869.png)

### 3.2 修改配置文件

```shell
sudo vi /etc/samba/smb.conf
```

在配置文件尾部添加下列配置信息，然后退出：

```shell
[samba]
   comment = samba home direction
   path = /usr/samba
   browseable = yes
   writeable = yes
   available = yes
   valid users = xiaozy
   write list = xiaozy
   directory mask = 0775
   create mask = 0775
```

参数说明：

```
# 共享资源名称
[samba]
   # 共享资源说明
   comment = samba home direction
   # 共享目录的路径，请确保该路径是存在的
   path = /usr/samba
   # 目录是否可读
   browseable = yes
   # 目录是否可写
   writeable = yes
   # 
   available = yes
   # 允许登录的用户，多个用户以逗号隔开，组名前要加@，例如use1,user2,@group1
   valid users = xiaozy
   # 禁止登录的用户
   # invalid users = 
   # 允许在此目录写入内容的用户，多个用户以逗号隔开，组名前要加@，例如use1,user2,@group1
   write list = xiaozy
   #
   directory mask = 0775
   #
   create mask = 0775
```

## 4.重启Samba

```shell
sudo /etc/init.d/samba restart
```

![image-20210526182931358](markdown/windows挂载linux共享目录.assets/image-20210526182931358.png)

## 5.添加Samba用户名

```shell
sudo smbpasswd -a xiaozy
```

![image-20210526183126989](markdown/windows挂载linux共享目录.assets/image-20210526183126989.png)

需要注意，此处可能抛出 <font color = red>Failed to add entry for user</font> <font color = blue>xxx</font> 的错误信息：

![image-20210526183607596](markdown/windows挂载linux共享目录.assets/image-20210526183607596.png)

这是因为Samba只能将Linux系统账号添加为Samba用户，此时需要在系统添加相关账号。

## 6.获取ip地址

```shell
ifconfig
```

![image-20210526183940175](markdown/windows挂载linux共享目录.assets/image-20210526183940175.png)

## 7.配置windows

“我的电脑” -> 右键 -> “映射网络驱动器”：

![image-20210526184330358](markdown/windows挂载linux共享目录.assets/image-20210526184330358.png)

输入地址：\\\ + 服务器ip地址 + \ + Samba共享资源名称

![image-20210526184437294](markdown/windows挂载linux共享目录.assets/image-20210526184437294.png)

登录Samba账号：

![image-20210526190304324](markdown/windows挂载linux共享目录.assets/image-20210526190304324.png)

![image-20210526190418272](markdown/windows挂载linux共享目录.assets/image-20210526190418272.png)

## 8.验证

linux在该共享文件夹在创建一个txt文件：

![image-20210526191650884](markdown/windows挂载linux共享目录.assets/image-20210526191650884.png)

window从该共享文件夹打开该txt文件，正常打开，说明”读权限“配置正常：

![image-20210526191729178](markdown/windows挂载linux共享目录.assets/image-20210526191729178.png)

windows，修改并保存该txt文件：

![image-20210526191947062](markdown/windows挂载linux共享目录.assets/image-20210526191947062.png)

出现权限提示，说明”写权限“配置出现问题，可能的原因有2个：

1.  Samba没有配置好写权限
2.  Linux没有放开该文件夹的写权限

在Linux查看该文件夹的权限信息：

![image-20210526192516443](markdown/windows挂载linux共享目录.assets/image-20210526192516443.png)

修改文件夹访问权限：

```shell
sudo chmod -R 777 ./samba/
```

![image-20210526192645899](markdown/windows挂载linux共享目录.assets/image-20210526192645899.png)

重新尝试在windows修改共享文件夹下的文件：

（保存成功）

在linux查看编辑后的文件，可以看到windows上编辑的内容：

![image-20210526192834078](markdown/windows挂载linux共享目录.assets/image-20210526192834078.png)