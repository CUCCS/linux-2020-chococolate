# Shell脚本编程进阶实验

## 实验环境

* ubuntu-18.04-server 64bit
   * 工作主机：wmy@chococolate，IP：192.168.56.101
   * 目标主机：u2@ubunbun，IP：192.168.56.102

## 实验要求
   - [x] FTP
     * 对照第6章课件中的要求选择一款合适的FTP服务器软件支持所有任务要求
   - [x] NFS
     * 对照第6章课件中的NFS服务器配置任务
   - [x] DHCP
     * 2台虚拟机使用Internal网络模式连接，其中一台虚拟机上配置DHCP服务，另一台服务器作为DHCP客户端，从该DHCP服务器获取网络地址配置
   - [ ] Samba
     * 对照第6章课件中smbclient一节的3点任务要求完成Samba服务器配置和客户端配置连接测试
   - [x] DNS
     * 基于上述Internal网络模式连接的虚拟机实验环境，在DHCP服务器上配置DNS服务，使得另一台作为DNS客户端的主机可以通过该DNS服务器进行DNS查询
    在DNS服务器上添加`zone "cuc.edu.cn"` 的以下解析记录

           ns.cuc.edu.cn NS
           ns A <自行填写DNS服务器的IP地址>
           wp.sec.cuc.edu.cn A <自行填写第5章实验中配置的WEB服务器的IP地址>
           dvwa.sec.cuc.edu.cn CNAME wp.sec.cuc.edu.cn

## 实验过程

* **配置工作主机SSH免密登录目标主机**

  * 目标主机SSH配置
    * 修改ssh配置文件`sudo vim /etc/ssh/sshd_config`

          #设置可通过口令认证SSH
          PasswordAuthentication yes
          #允许root用户登录
          PermitRootLogin yes

    * 重启ssh服务`sudo systemctl restart ssh`

  * 工作主机生成ssh-key

         #fur为key的名称
         $ ssh-keygen 

         #将公钥传输至目标主机authorized_keys文件
         $ ssh-copy-id -i ~/.ssh/id_rsa.pub u2@192.168.56.102


  * 设置免密登录
    * 取消root口令并禁用口令登录

          #修改目标主机配置文件
          $ sudo vim /etc/ssh/sshd_config
               PasswordAuthentication no
               PermitRootLogin without-password

          #重启ssh服务
          $ sudo systemctl restart ssh

  * root免密登录：`ssh  u2@192.168.56.102`
   ![免密登陆结果展示](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/1.png)

* **FTP**
  * 服务器选择：vsftpd
  * 脚本文件：[vsftpd.sh](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.sh/dhcp.sh))
  * 配置文件：[/etc/vsftpd.conf](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.conf/vsftp.conf)
  * 通过工作主机运行脚本在目标主机安装vsftpd并完成相关配置

    * 将脚本文件`vsftpd.sh`拷贝到目标主机：`scp -i ~/.ssh/id_rsa -r /home/wmy/vsftpd.sh u2@192.168.56.102:workspace/`
    ![传输结果展示](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/2.png)

    * 借助SSH远程登陆目标主机，执行：`bash workspace/vsftpd.sh'`
    ![运行脚本结果展示](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/3.png)

  * 配置一个提供匿名访问的FTP服务器，匿名访问者可以访问1个目录且仅拥有该目录及其所有子目录的只读访问权限
   ![anonymous](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/4.png)

  * 配置一个支持用户名和密码方式访问的账号，该账号继承匿名访问者所有权限，且拥有对另1个独立目录及其子目录完整读写（包括创建目录、修改文件、删除文件等）权限

    * 用户名密码登录
     ![chococolate_login](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/5.png)

    * 在chococolate用户的目录下出现了匿名用户目录下的文件，可证明继承匿名者访问权限
     ![继承权限](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/6.png)

    * 删除文件、创建目录、修改文件 & FTP用户不能越权访问指定目录之外的任意其他目录和文件（注意：不是通过 绝对路径 方式来遍历，而是验证通过 相对路径 （例如 cd ../../../../ ）方式无法翻出 FTP 服务器指定的当前用户的 家目录 访问到 家目录及其所有子目录 以外的任何其他目录）
     ![dmr&越权访问](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/7.png)

    * 匿名访问权限仅限白名单IP来源用户访问，禁止白名单IP以外的访问
     ![拒绝访问](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/8.png)

  * 参考
    * [vsftpd](https://security.appspot.com/vsftpd.html)
    * [FTP Server && vsftpd-FTP Server](https://ubuntu.com/server/docs/service-ftp)


* **NFS**
  * 脚本文件
    * [nfs_s.sh]([scripts/nfs_srv.sh](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.sh/nfs_s.sh))
    * [nfs_c.sh](s[cripts/nfs_clt.sh](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.sh/nfs_c.sh))
  * 配置文件
    * [exports.conf](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.conf/exports.conf)
  * 将nfs_s.sh拷贝到目标主机，工作主机运行nfs_srv.sh脚本
   `scp -i ~/.ssh/id_rsa -r /home/wmy/nfs_s.sh u2@192.168.56.102:workspace/nfs_s.sh`、
   `ssh -i ~/.ssh/id_rsa -r u2@192.168.56.102 'bash workspace/nfs_s.sh'`

        #在目标主机通过进程查看nfs服务是否运行
         ps -aux|grep -v grep|grep nfs

  * 工作主机运行nfs_c.sh脚本 `bash nfs_c.sh`
  * 在1台Linux上配置NFS服务，另1台电脑上配置NFS客户端挂载2个权限不同的共享目录，分别对应只读访问和读写访问权限
    * 创建的两个目录分别为:只读`/nfs/gen_r`和读写`/nfs/gen_rw`
      ![目录挂载详情](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/10.png)
  * 客户端共享目录中文件、子目录的属主、权限信息和在NFS服务器端的信息,uid和gid一致
    * client
      ![client](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/11.png)
    * server
      ![server](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/12.png)

  * 参照资料
    > By default, NFS translates requests from a root user remotely into a non-privileged user on the server. This was intended as security feature to prevent a root account on the client from using the file system of the host as root. no_root_squash disables this behavior for certain shares.
     * 添加两个/home下的共享目录，分别设置`no_root_squash`和不设置`no_root_squash`

     * 对于设置了no_root_squash的共享目录及对于没有设置no_root_squash的共享目录，无法在工作主机(client)写入文件，创建目录
      ![rsquash&no_rsquash](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/13.png)
      

  * 参考[how-to-set-up-an-nfs-mount-on-ubuntu-18-04](https://www.digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-18-04)

* **DHCP**
  * 脚本文件 [dhcp.sh](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.sh/dhcp.sh)
  * 配置文件 
    * [dhcpd.conf](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.conf/dhcpd.conf)
    

  * 2台虚拟机使用Internal网络模式连接，其中一台虚拟机上配置DHCP服务，另一台服务器作为DHCP客户端，从该DHCP服务器获取网络地址配置
   > Internal模式:内网模式，即虚拟机与外网完全断开，只实现虚拟机于虚拟机之间的内部网络模式。
  
    * 先将两台虚拟机增加网卡为`内部网络`网络模式,`client`:工作主机；`server`:目标主机
      * 工作主机名称client_internet，目标主机名称server_internet

    * server配置
      * 通过`scp`将脚本`dhcp.sh`拷贝到目标主机，通过`ssh`方式远程执行脚本`sudo bash dhcp.sh`
      * 查看server配置文件![server_yaml](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/14.png)
    * client配置
      * 修改配置文件`/etc/netplan/01-netcfg.yaml`，添加`enp0s9`,设置`dhcp4: yes`
      * `sudo netplan apply`使配置生效
      * 查看client配置文件并添加enps09：dhcp4:yes
    * 实验结果
    ![dhcp_ip](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/15.png)
  * 参考[isc-dhcp-server](https://help.ubuntu.com/community/isc-dhcp-server)

* **DNS**
  * 基于上述Internal网络模式连接的虚拟机实验环境，在DHCP服务器上配置DNS服务，使得另一台作为DNS客户端的主机可以通过该DNS服务器进行DNS查询
  * 在DNS服务器上添加 zone "cuc.edu.cn" 的解析记录

  * server

    * 安装bind9：`sudo apt update && sudo apt install bind9`
    * 修改配置文件`sudo vim /etc/bind/named.conf.options`

          #在options选项中添加以下配置
          listen-on { 192.168.56.102; };  # ns1 private IP address - listen on private network only
          allow-transfer { none; }; # disable zone transfers by default
          forwarders {
             8.8.8.8;
              8.8.4.4;
          };
    * 编辑配置文件`sudo vim /etc/bind/named.conf.local`

          #添加如下配置
          zone "cuc.edu.cn" {
              type master;
              file "/etc/bind/db.cuc.edu.cn";
          };
    * 生成配置文件`db.cuc.edu.cn`

          $ sudo cp /etc/bind/db.local /etc/bind/db.cuc.edu.cn
    * 编辑配置文件`sudo vim /etc/bind/db.cuc.edu.cn`
    * 重启bind9：`sudo service bind9 restart`

  * client
     * 安装resolvconf：`sudo apt update && sudo apt install resolvconf`

     * 修改配置文件：`sudo vim /etc/resolvconf/resolv.conf.d/head`

            #添加配置
            search cuc.edu.cn
            nameserver 192.168.57.1
      * sudo resolvconf -u
  * 测试结果：
    ![client](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/17.png)
    ![server](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/images/16.png)

  * 参考[DNS_Service](https://ubuntu.com/server/docs/service-domain-name-service-dns)

* **Samba**

  * **在windows 10客户端上连接Linux上的Samba服务器**
    * 脚本文件 [smbd.sh](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.sh/smbd.sh)
    * 配置文件 [/etc/samba/smb.conf](https://github.com/CUCCS/linux-2020-chococolate/blob/chap0x06/.conf/smb.conf)

    * 执行脚本smb_srv.sh
    > 配置文件目录：`/etc/samba/smb.conf`
    > 服务启动\停止\重启等命令：`/etc/init.d/smbd {start|stop|reload|restart|force-reload|status}`
    * 打开资源管理器，右键“此电脑”，选择“添加一个网络位置”
    * 输入共享文件夹路径
    * 访问匿名目录，不用输入账号密码，且不可以创建文件
    * 访问指定用户文件夹，需要输入账号密码，且可以创建文件夹
   
  * **在Linux上连接Windows10上的服务器**
  > sudo apt install smbclient
    * 设置Windows共享文件夹，Linux访问Windows匿名共享目录，下载目录。
  * 参考
    * [how-to-share-files-between-windows-and-linux](https://www.howtogeek.com/176471/how-to-share-files-between-windows-and-linux/)
    * [getting-whole-folder-using-smbclient](https://indradjy.wordpress.com/2010/04/14/getting-whole-folder-using-smbclient/)


## 遇到的问题

* 1.在windows端写的bash在linux运行会报错，应为尾行多了^M(以dhcp.sh为例子)
  解决方案：
  `vim dhcp.sh` 
  `:set fileformat=unix`
  `:wq`
* 2.在具体实验中，使用FTP连接方式时增删目录时，发现只能删除原有文件，对于新建文件可以重命名，但不能进行删除操作（不太懂这个逻辑==）
* 3.参考哥姐脚本代码samba.sh硬是跑不通，报的错为：`unable to locate package samba`,运行了`apt-get update`不解决问题，一顿搜索后发现可能是主机欸之问题（加了中科大的源还是不行，劝退了555）