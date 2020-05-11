### 虚拟机环境



1. ##### 单台虚拟机：内存4G，硬盘50G，安装必要环境(最小化安装)

   ~~~shell
   sudo yum install -y epel-release
   sudo yum install -y psmisc nc net-tools rsync vim lrzsz ntp libzstd openssl-static tree iotop
   ~~~

2. ##### 更改虚拟机静态IP(按照自己机器的网络设置进行修改)

   ~~~shell
   sudo vim /etc/sysconfig/network-scripts/ifcfg-ens33
   ~~~

   ~~~shell
   #更改ifcfg-ens33里的内容为：
   DEVICE=ens33
   TYPE=Ethernet
   ONBOOT=yes
   BOOTPROTO=static
   NAME="ens33"
   IPADDR=192.168.117.102
   PREFIX=24
   GATEWAY=192.168.117.2
   DNS1=192.168.117.2
   DNS2=8.8.8.8
   ~~~

3. ##### 修改主机名、映射

   改主机名：

   ~~~shell
   #方式一 改为：hadoop102 
   sudo vim /etc/hostname
   
   #方式二 或用以下命令直接改
   hostnamectl --static set-hostname hadoop102
   ~~~

   改映射：

   ~~~shell
   #配置主机名称映射，打开/etc/hosts
   sudo vim /etc/hosts
   ~~~

   ~~~shell
   #虚拟机的hosts里都加入：
   192.168.117.102 hadoop102
   192.168.117.103 hadoop103
   192.168.117.104 hadoop104
   ~~~

4. ##### 关闭防火墙

   ~~~shell
   sudo systemctl stop firewalld
   sudo systemctl disable firewalld
   ~~~

5. ##### 创建nogc用户

   ~~~shell
   sudo useradd nogc
   sudo passwd  nogc
   ~~~

6. ##### 配置nogc用户具有root权限

   ~~~shell
   #修改/etc/sudoers文件，找到下面一行（98行），在root下面添加一行，如下所示
   sudo vi /etc/sudoers
   
   ## Allow people in group wheel to run all commands
   root    ALL=(ALL)     ALL
   nogc   ALL=(ALL)  NOPASSWD:ALL
   ~~~

7. ##### SSH无密登录配置

   （1）在hadoop102上生成公钥和私钥：

   ```
   ssh-keygen -t rsa
   ```

   然后敲（三个回车），就会生成两个文件id_rsa（私钥）、id_rsa.pub（公钥）

   （2）将公钥拷贝到要免密登录的目标机器上

   ```
   ssh-copy-id hadoop102
   ssh-copy-id hadoop103
   ssh-copy-id hadoop104
   ```

   （3）分别在hadoop103、和hadoop104上执行以上两个步骤

   注意：

   root和其它用户不共享免密，所以想要在root下也实现无密，要在root上重新配置。

