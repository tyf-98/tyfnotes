# MySQL安装



> IT界共识，求稳不求新，不要安装最新版本软件，会有很多兼容性问题，目前在企业里面普遍使用的是5.7左右的版本



### MacOS系统

1. 下载

```python
# 官网下载地址：https://downloads.mysql.com/archives/community/
```

![截屏2023-05-24 09.26.27](MySQL安装.assets/截屏2023-05-24 09.26.27.png)

2. 解压

   ```bash
   # 双击即可解压，解压后移动到/usr/local目录，并重命名成mysql：
   sudo mv /Users/fei/Downloads/mysql-5.7.24-macos10.14-x86_64 /usr/local/mysql
   ```
   
   

3. 配置环境变量

   ```bash
   # 把bin目录和support-files目录加入环境变量，bin里面的mysql为客户端，support-files里面的mysql.server为服务端
   
   # 1、打开终端，编辑环境变量配置文件
   sudo vim /etc/profile
   
   
   # 2、打开配置文件之后，按i进入编辑模式，把光标移动到最后，在最后做如下配置：
   export PATH=/usr/local/mysql/bin:/usr/local/mysql/support-files:$PATH
   
   # 编辑之后保存(冒号和感叹号都要输入)
   # 按esc，输入:wq!
   
   # 3、重新加载环境变量配置，使前面的配置生效
   source /etc/profile
   # 但是终端关闭之后，重新打开，配置的环境变量又会失效,需要重新执行这个命令，因为/etc/profile是系统级别的环境变量配置文件，可以在用户目录～下新建一个.zshrc文件，在里面写上source /etc/profile，这样的话终端启动之后，就会自动加载这个文件，从而执行source /etc/profile，这样就没问题了
   vim ~/.zshrc
   #里面写上如下配置：
   source /etc/profile
   ```
   
   
   
3. 初始化数据库

   ```bash
   # 4、初始化数据库
   sudo mysqld --initialize --user=mysql
   # 执行完此命令之后，最后会有如下提示：'root@localhost: >wP)6AONtF=o', 冒号后面就是数据库的初始密码，一定要复制出来记住
   ```
   
   如果安装过程中出现如下提示，就点击取消（后续遇到类似情况，就这样操作）：
   
   ![截屏2023-05-23 11.03.58](MySQL安装.assets/截屏2023-05-23 11.03.58.png)
   
   然后打开设置->隐私与安全性->仍然允许（后续遇到类似情况，就这样操作）：
   
   ![截屏2023-05-23 11.04.47](MySQL安装.assets/截屏2023-05-23 11.04.47.png)
   
   最后运行的时候点打开： 
   
   ![截屏2023-05-23 11.11.58](MySQL安装.assets/截屏2023-05-23 11.11.58.png)



4. 启动

   ```bash
   
   # 1、启动服务
   sudo mysql.server start
   
   # 2、连接mysql服务端
   mysql -h localhost -P 3306 -u root -p
   mysql -u root -p	# 连接本机也可以省略地址：
   # 然后输入密码，就是前面初始化的时候复制的那个密码
   
   # 4、设置密码
   ALTER USER 'root'@'localhost' IDENTIFIED BY '123';
   # 把root用户的密码重置为123，重置之后之前复制的密码就可以不用记了，首次连接是用不了update语句的，只能用ALTER修改密码
   
   # 5、忘记密码
   sudo mysqld_safe --skip-grant-tables    # 跳过授权验证启动服务端
   mysql -uroot -p	#跳过验证启动的服务端，可以免密码登陆
   update mysql.user set authentication_string=password('123') where user='root' and host = 'localhost';	# 修改密码
   flush privileges; # 刷新权限
   
   ps -ef | grep msyqld	# 查看mysqld的进程号
   sudo kill -9 进程号	# 杀掉跳过授权验证启动的服务端
   sudo mysql.server start	# 启动正常的服务端
   
   # 6、第一条sql语句，查看MySQL数据库列表：
   show databases;
   
   # 6、退出数据库连接
   exit
   ```
   
5. 统一编码

   ```python
   # 修改配置文件即可
   sudo vim /etc/my.cnf
   
   # 做如下配置：
   [mysqld]
   character-set-server=utf8
   collation-server=utf8_general_ci
   [client]
   default-character-set=utf8
   [mysql]
   default-character-set=utf8
   ```

   

### windows系统

1. 下载

   ```python
   # 官网下载地址：
   """
   https://downloads.mysql.com/archives/community/
   """ 
   ```

   ![截屏2023-05-24 15.13.19](MySQL安装.assets/截屏2023-05-24 15.13.19.png)

2. 解压

   ```python
   # 解压之后，放到合适的目录，但路径不要有中文：
   ```

4. 配置环境变量

   ```python
   # 把bin路径配置在环境变量
   ```

5. 初始化数据库

   ```shell
   mysqld --initialize-insecure --user=mysql
   ```

6. 启动服务端

   ```shell
   mysqld
   ```

7. 连接mysql

   ```shell
   mysql -u root -p
   ```

7. 重置密码

   ```bash
   mysqladmin -uroot -p原密码 password 123  # 重置密码
   
   # 忘记密码
   mysqld --skip-grant-tables	# 跳过授权验证启动服务端
   mysql -uroot -p	#跳过验证启动的服务端，可以免密码登陆
   update mysql.user set authentication_string=password('123') where user='root'and host = 'localhost';	# 修改密码
   flush privileges; # 刷新权限
   
   tasklist | findstr mysql			 # 查看占用mysql程序的进程
   netstat -ano | findstr 3306    # 查看占用3306端口的进程
   taskkill /F /PID 进程ID    # 杀掉该进程
   mysqld	# 启动正常的服务端
   ```

8. 添加mysql服务

   ```bash
   # 如果之前启动过mysqld，需要先杀掉该进程，不然端口占用，服务无法启动成功，前面有介绍怎么杀掉进程
   
   mysqld -install	# 添加服务
   mysqld -remove  # 移除服务
   
   net start mysql # 启动服务
   net stop mysql	# 停止服务
   ```

9. 统一编码

   ```python
   # 在mysql根目录下新建my.ini
   
   # 做如下配置：
   [mysqld]
   character-set-server=utf8
   collation-server=utf8_general_ci
   [client]
   default-character-set=utf8
   [mysql]
   default-character-set=utf8
   ```

   

### Linux系统(centos7)

1. 直接安装

   ```bash
   yum install mysql-community-server -y
   # 如果成功，则不需要安装MySQLyum源
   # 如果出现以下错误，则需要安装MySQL yum源：'Loading mirror speeds from cached hostfile 没有可用软件包 mysql-community-server。'
   ```

2. 安装MySQL yum源（如果第一步安装成功，则不需要此操作）

   - 打开mysql官网：https://dev.mysql.com/downloads/repo/yum/

   - 根据自己的系统版本选择安装包

     ![截屏2023-05-27 10.26.40](MySQL安装.assets/截屏2023-05-27 10.26.40.png)

   - 点击Donwload，进来之后，拷贝下载链接

     ![截屏2023-05-27 10.24.38](MySQL安装.assets/截屏2023-05-27 10.24.38.png)

     https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm

   - 使用wget命令下载MySQL yum源

     ```bash
     wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
     
     # 如果出现如下报错，说明wget没有安装：'-bash: wget: 未找到命令'，安装一下即可
     yum install wget -y
     ```

   - 安装安装MySQL的yum源

     ```bash
     rpm -ivh mysql80-community-release-el7-7.noarch.rpm
     ```

   - 查看MySQL yum源的安装包

     ```bash
     yum repolist all | grep mysql
     # 可以看到，默认启用的安装源是mysql80-community版本，
     # 但我们想安装的是mysql57-community版本
     ```

     ![截屏2023-05-26 19.47.20](MySQL安装.assets/截屏2023-05-26 19.47.20.png)

   - 修改MySQL yum源的默认安装包

     ```bash
     yum-config-manager --disable mysql80-community # 取消默认安装mysql80-community版本
     yum-config-manager --enable mysql57-community # 设置默认安装mysql57-community版本
     
     # 执行上面两条命令过程中，如果出现如下报错，说明系统缺少yum-utils工具："-bash: yum-config-manager: command not found"
     # yum-config-manager 命令是 yum-utils 包中的一部分，需要先安装 yum-utils。
     yum install yum-utils
     # 安装完成后，再尝试执行 yum-config-manager 命令即可。
     ```

   - 安装5.7版本

     修改完MySQL yum源之后，再次执行：yum repolist all | grep mysql ，会发现默认启用版本已经变成了5.7

     ![截屏2023-05-26 19.45.47](MySQL安装.assets/截屏2023-05-26 19.45.47.png)

     执行安装命令即可：

     ```bash
     yum install mysql-community-server -y
     ```

3. 启动mysql服务端

   ```bash
   启动mysql服务
   systemctl start mysqld.service 	# 启动mysql服务
   systemctl restart mysqld.service # 重启mysql服务
   systemctl stop mysqld.service 	# 停止mysql服务
   systemctl status mysqld.service # 查看mysql状态
   ```

4. 重置密码

   新安装的mysql服务端会有一个临时密码，存放在/var/log/mysqld.log中，可以通过下方命令查看：

   ```bash
   grep password /var/log/mysqld.log
   ```

   root@localhost: 后面就是密码，查看到密码之后，进行登录，然后修改密码

   ```bash
   mysql -u root -p
   # 输入前面查看到的临时密码
   
   # 修改临时密码（首次连接是用不了update语句的，只能用ALTER修改密码）
   ALTER USER USER() IDENTIFIED BY '123';
   # 如果出现如下提示：'ERROR 1819 (HY000): Your password does not satisfy the current policy requirements'
   # 则说明系统要求的密码强度较高，请尝试输入更复杂的密码，有的需要包含符号、大写字母、小写字母、数字，以及长度可能也有限制，就尽量输入复杂的密码。
   ```

5. 忘记密码

   ```bash
   # 在 MySQL 配置文件中禁用密码教研
   vim /etc/my.cnf
   # 如果找不到vim命令，安装一下即可：'sudo: vim：找不到命令'
   yum install vim -y
   
   # 将 skip-grant-tables 选项添加到 [mysqld] 部分中, 然后重启mysql服务
   systemctl restart mysqld.service
   
   mysql -uroot -p	#跳过验证启动的服务端，可以免密码登陆
   update mysql.user set authentication_string=password('123') where user='root'and host = 'localhost';	# 跳过验证修改密码,没有密码强度限制
   flush privileges; # 刷新权限
   
   # 配置文件修改回原样，重启mysql服务
   systemctl restart mysqld
   ```

6. 统一编码(和mac一样)

   ```python
   # 修改配置文件即可
   vim /etc/my.cnf
   
   # 给/etc/my.cnf 做如下配置：
   [mysqld]
   character-set-server=utf8
   collation-server=utf8_general_ci
   [client]
   default-character-set=utf8
   [mysql]
   default-character-set=utf8
   ```


### 连接MySQL

- 本地连接

  ```bash
  mysql -u root -p
  ```

- 远程连接

  ```sql
  # 修改用户的host权限
  select user,host from mysql.user; # 查看msyql库里面的user表的user字段和host字段，默认都只允许localhost登陆，也就是默认只允许本地登陆
  update mysql.user set host='%' where user ='root';	# 把root用户的host权限改成%，表示所有host都可以登陆root帐号
  # ！！！此操作要谨慎，root用户拥有所有权限，不建议把它的host改成%，最好新建一个普通权限用户，我们这里是学习，这样改无所谓。
  
  flush privileges;	# 改完之后刷新一下权限
  select user,host from mysql.user; 	# 重新查看权限，root用户的host就变成%了
  
  # 现在就可以远程连接了
  mysql -h ip地址 -P 3306 -u root -p # 可以不写-P参数，默认就是3306，如果是云服务器的话，需要开放3306端口
  ```




# MySQL5.7 and MySQL8.0安装

> 网盘地址：https://active.clewm.net/FrcyFA



### 1、安装mysql的yum源

```bash
# ----------- 安装mysql5.7 yum源 -----------注：CentOS 9不支持MYSQL5.7的仓库，以下安装5.7都是在CentOS 7上进行的
yum install wget -y
wget https://dev.mysql.com/get/mysql80-community-release-el7-7.noarch.rpm
rpm -ivh mysql80-community-release-el7-7.noarch.rpm

# 切换57版本,也可以直接编辑此文件：/etc/yum.repos.d/mysql-community.repo
yum repolist all | grep mysql
yum install yum-utils
yum-config-manager --disable mysql80-community # 取消默认安装mysql80-community版本
yum-config-manager --enable mysql57-community # 设置默认安装mysql57-community版本


# ----------- 手动安装mysql5.7 yum源 -----------
# 如果安装时出现这个报错：获取 GPG 密钥失败，就把所有gpgcheck改成0，再重写安装即可

# 在配置文件做如下配置:
vi /etc/yum.repos.d/mysql-community.repo

# Enable to use MySQL 5.7
[mysql57-community]
name=MySQL 5.7 Community Server
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-5.7-community-el7-x86_64/
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

[mysql-connectors-community]
name=MySQL Connectors Community
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-connectors-community-el7-x86_64/
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql

[mysql-tools-community]
name=MySQL Tools Community
baseurl=https://mirrors.tuna.tsinghua.edu.cn/mysql/yum/mysql-tools-community-el7-x86_64/
enabled=1
gpgcheck=0
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql-2022
       file:///etc/pki/rpm-gpg/RPM-GPG-KEY-mysql




# ----------- 安装mysql8.0官网yum源 -----------
yum install wget -y
wget https://dev.mysql.com/get/mysql80-community-release-el9-3.noarch.rpm
rpm -ivh mysql80-community-release-el9-3.noarch.rpm
```



### 2、安装及配置

```BASH
# 安装mysql（如果出现错误：Cannot find a valid baseurl，见下方4.1）
yum install mysql-community-server -y

# 启动并获取初始密码
systemctl start mysqld
grep password /var/log/mysqld.log
7xTs(trouqaS

# mysql5.7设置校验密码强度
set global validate_password_policy=0;
set global validate_password_length=4;

# mysql8.0设置校验密码强度，必须要先修改一个复杂密码，才能做这个操作）
set global validate_password.policy=0;
set global validate_password.length=4;

# 修改密码
ALTER USER USER() IDENTIFIED BY '1234';
update mysql.user set host='%' where user ='root';
flush privileges;


# 修改配置文件
vi /etc/my.cnf
[mysqld]
character-set-server=utf8mb4
collation-server=utf8mb4_general_ci

[client]
default-character-set=utf8mb4
[mysql]
default-character-set=utf8mb4
user=root
password=1234

# 重启服务
systemctl restart mysqld
```



### 3、防火墙开放3306端口

```bash
# 1.检查firewalld服务是否正在运行：
systemctl status firewalld

# 2.如果firewalld服务没有运行，可以使用以下命令启动它：
systemctl start firewalld

# 3.查看防火墙规则
firewall-cmd --list-all

# 4.查看3306端口是否开放
firewall-cmd --zone=public --query-port=3306/tcp

# 5.开放3306端口：
firewall-cmd --zone=public --add-port=3306/tcp --permanent

# 6.重新加载firewalld配置：
firewall-cmd --reload
```



### 4、其他问题

4.1 Cannot find a valid baseurl

```bash
# 如果出现如下错误，解决方法：
# Cannot find a valid baseurl for repo: base/7/x86_64

# 在配置文件做如下配置即可:
vi /etc/resolv.conf

# Generated by NetworkManager
nameserver 223.5.5.5
search localdomain
```



4.2 SSL connection error

```bash
# 如果远程连接MySQL出现以下问题，说明客户端和服务端SSL协议版本不匹配，原因是客户端我们之前安装的是MySQL5.7.24，现在服务端是MySQL5.7.43
RROR 2026 (HY000): SSL connection error: protocol version mismatch

# 方法1：
# 在配置文件做如下配置即可:
vi /etc/my.cnf

[mysqld]
...
ssl-cert=/path/to/server-cert.pem
ssl-key=/path/to/server-key.pem
ssl-ca=/path/to/ca-cert.pem
...

# 重启服务
systemctl restart mysqld

# 此方法对MySQL5.7有效（上面配置的3个路径都是无效路径，配置任意无效路径都可以跳过校验）
# 8.0测试：MySQL8.0.34（mac），需要在服务端机器登录一次之后才可以远程登录（且重启和改密之后，也需要在服务端机器登陆一次才可以远程登录），其它系统或者其他8以上版本未测试


# 方法2（MySQL8.0.34（mac）和方法1一样，同样需要在服务端机器登录一次才可以远程登录）：
mysql -uroot -p1234 -h192.168.3.176 --ssl-mode=disabled

# 以上两种方法会导致ssl失效，会降低数据传输的安全性，最好是客户端和服务端版本保持统一，或者使用pycharm插件作为客户端，就不存在这个问题。
```

> 网盘地址：https://active.clewm.net/FrcyFA

