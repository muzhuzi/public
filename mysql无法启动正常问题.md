![image](https://github.com/muzhuzi/public/assets/5387766/a21e9067-82aa-43e4-a809-6d61f84705cb)![image](https://github.com/muzhuzi/public/assets/5387766/82b49c93-6f00-417c-99f6-77db960f0852)## 通过mysql rpm包安装的mysql服务器端
## Linux意外重启后，mysql无法启动，报如下错误信息：
```
Can’t start server: can’t create PID filepath: No such file or directory
```

### 错误原因
一般是由于服务器强制关机导致pid文件丢失。

## 临时解决办法
1. 在/etc/my.cnf 中查看pid-file的位置：
eg: pid-file=/var/run/mysql/mysqld.pid

2. 创建对应的目录并修改权限：
```
mkdir -p /var/run/mysql
touch /var/run/mysql/mysqld.pid
chown -R mysql:mysql /var/run/mysql
systemctl start mysql
```

4. 查看mysql运行状态：
```
systemctl status mysql.service
```

### 终极原因分析：
通过rpm安装mysql时，权限获取不足。造成tmpfiles权限不足，重启时创建相应文件失败，导致mysql启动失败。

### 解决办法：
1. 查看tmpfiles的保存信息，找到对应的报错文件路径。
```
systemctl status systemd-tmpfiles-setup.service
```
![image](https://github.com/muzhuzi/public/assets/5387766/e056eefe-a59d-4ff1-8791-7a61fc4935a6)

2. 增加MySQL组在/etc/group中，以保证mysql正常启动
```
mysql:x:1012:
```

3. 验证重启后，mysql是否正常启动，重启系统：
```
systemctl reboot
```
