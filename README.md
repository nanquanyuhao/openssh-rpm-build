# openssh-rpm-build

## 条件

1. 构建操作系统：`CentOS 7.7`，使用`root`用户操作
2. 依赖软件包
   - `openssh-8.6p1.tar.gz`
   - `x11-ssh-askpass-1.2.4.1.tar.gz`
3. 提前具备安装软件：`yum install rpm-build zlib-devel openssl-devel gcc perl-devel pam-devel`
4. 桌面图形化涉及构建软件：`yum install libX11-devel libXt-devel imake gtk2-devel libedit-devel xauth`

## 构建过程

1. `root`用户登录，将项目`rpmbuild`文件夹置于`root`家目录
2. 执行`cd ~ && rpmbuild -ba rpmbuild/SPECS/openssh.spec`
3. 文件夹`/root/rpmbuild/RPMS/x86_64`下即构建产物

## 使用说明

1. 将构建产物呈现如下，即`8.6p1-1`的`openssh`服务`rpm`包：

   ```shell
   [root@localhost x86_64]# ll
   total 4852
   -rw-r--r--. 1 root root  666608 Jun 12 23:31 openssh-8.6p1-1.el7.x86_64.rpm
   -rw-r--r--. 1 root root   44336 Jun 12 23:31 openssh-askpass-8.6p1-1.el7.x86_64.rpm
   -rw-r--r--. 1 root root   25568 Jun 12 23:31 openssh-askpass-gnome-8.6p1-1.el7.x86_64.rpm
   -rw-r--r--. 1 root root  632756 Jun 12 23:31 openssh-clients-8.6p1-1.el7.x86_64.rpm
   -rw-r--r--. 1 root root 3128004 Jun 12 23:31 openssh-debuginfo-8.6p1-1.el7.x86_64.rpm
   -rw-r--r--. 1 root root  460944 Jun 12 23:31 openssh-server-8.6p1-1.el7.x86_64.rpm
   ```

   - `openssh-askpass-8.6p1-1.el7.x86_64.rpm`+`openssh-askpass-gnome-8.6p1-1.el7.x86_64.rpm`：开启支持桌面访问后追加的产物，没有图形化需要可以不安装；即使使用图形化也可无需安装，经测试原版本自带的`askpass`相关支持可以满足要求
   
2. 执行更新命令：

   ```shell
   [root@localhost x86_64]# rpm -Uvh openssh-*.rpm
   Preparing...                          ################################# [100%]
   Updating / installing...
      1:openssh-8.6p1-1.el7              ################################# [ 14%]
      2:openssh-clients-8.6p1-1.el7      ################################# [ 29%]
   new rpm before install
   backup old rpm config
   1./etc/ssh/sshd_config backup done.
   2./etc/pam.d/sshd backup done.
      3:openssh-server-8.6p1-1.el7       ################################# [ 43%]
   new rpm after install
   backup new config and restore old config
   backup new config done
   restore old config done and adjust PermitRootLogin
   remove all ssh_host_*
      4:openssh-debuginfo-8.6p1-1.el7    ################################# [ 57%]
   Cleaning up / removing...
      5:openssh-server-7.4p1-21.el7      ################################# [ 71%]
      6:openssh-clients-7.4p1-21.el7     ################################# [ 86%]
      7:openssh-7.4p1-21.el7             ################################# [100%]
   ```

   

3. 验证可用

## 问题

　　若版本过就，例如`CentOS 7.3`，自带`openssl`版本`OpenSSL 1.0.1e-fips`，使用`rpm`更新报如下：

```shell
[root@localhost x86_64]# rpm -Uvh openssh-*.rpm
error: Failed dependencies:
	libcrypto.so.10(OPENSSL_1.0.2)(64bit) is needed by openssh-8.6p1-1.el7.x86_64
	libcrypto.so.10(OPENSSL_1.0.2)(64bit) is needed by openssh-clients-8.6p1-1.el7.x86_64
	libcrypto.so.10(OPENSSL_1.0.2)(64bit) is needed by openssh-server-8.6p1-1.el7.x86_64
```

　　此处考虑`yum`安装即可，如：

```shell
[root@localhost x86_64]# yum install openssh-*.rpm
Loaded plugins: fastestmirror, priorities
Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
Examining openssh-8.6p1-1.el7.x86_64.rpm: openssh-8.6p1-1.el7.x86_64
Marking openssh-8.6p1-1.el7.x86_64.rpm as an update to openssh-6.6.1p1-31.el7.x86_64
Examining openssh-clients-8.6p1-1.el7.x86_64.rpm: openssh-clients-8.6p1-1.el7.x86_64
Marking openssh-clients-8.6p1-1.el7.x86_64.rpm as an update to openssh-clients-6.6.1p1-31.el7.x86_64
Examining openssh-debuginfo-8.6p1-1.el7.x86_64.rpm: openssh-debuginfo-8.6p1-1.el7.x86_64
Marking openssh-debuginfo-8.6p1-1.el7.x86_64.rpm to be installed
Examining openssh-server-8.6p1-1.el7.x86_64.rpm: openssh-server-8.6p1-1.el7.x86_64
Marking openssh-server-8.6p1-1.el7.x86_64.rpm as an update to openssh-server-6.6.1p1-31.el7.x86_64
Resolving Dependencies
--> Running transaction check
---> Package openssh.x86_64 0:6.6.1p1-31.el7 will be updated
---> Package openssh.x86_64 0:8.6p1-1.el7 will be an update
--> Processing Dependency: libcrypto.so.10(OPENSSL_1.0.2)(64bit) for package: openssh-8.6p1-1.el7.x86_64
base                                                                                                                                              | 3.6 kB  00:00:00     
epel/x86_64/metalink                                                                                                                              | 5.6 kB  00:00:00     
epel                                                                                                                                              | 4.7 kB  00:00:00     
extras                                                                                                                                            | 2.9 kB  00:00:00     
updates                                                                                                                                           | 2.9 kB  00:00:00     
(1/7): base/7/x86_64/group_gz                                                                                                                     | 153 kB  00:00:02     
(2/7): epel/x86_64/updateinfo                                                                                                                     | 1.0 MB  00:00:03     
(3/7): extras/7/x86_64/primary_db                                                                                                                 | 242 kB  00:00:02     
(4/7): epel/x86_64/group_gz                                                                                                                       |  96 kB  00:00:05     
(5/7): base/7/x86_64/primary_db                                                                                                                   | 6.1 MB  00:00:07     
(6/7): epel/x86_64/primary_db                                                                                                                     | 6.9 MB  00:00:07     
(7/7): updates/7/x86_64/primary_db                                                                                                                | 8.0 MB  00:00:05     
Determining fastest mirrors
 * base: mirrors.aliyun.com
 * epel: mirrors.bfsu.edu.cn
 * extras: mirrors.aliyun.com
 * updates: mirrors.aliyun.com
---> Package openssh-clients.x86_64 0:6.6.1p1-31.el7 will be updated
---> Package openssh-clients.x86_64 0:8.6p1-1.el7 will be an update
---> Package openssh-debuginfo.x86_64 0:8.6p1-1.el7 will be installed
---> Package openssh-server.x86_64 0:6.6.1p1-31.el7 will be updated
---> Package openssh-server.x86_64 0:8.6p1-1.el7 will be an update
--> Running transaction check
---> Package openssl-libs.x86_64 1:1.0.1e-60.el7 will be updated
--> Processing Dependency: openssl-libs(x86-64) = 1:1.0.1e-60.el7 for package: 1:openssl-1.0.1e-60.el7.x86_64
---> Package openssl-libs.x86_64 1:1.0.2k-21.el7_9 will be an update
--> Running transaction check
---> Package openssl.x86_64 1:1.0.1e-60.el7 will be updated
---> Package openssl.x86_64 1:1.0.2k-21.el7_9 will be an update
--> Finished Dependency Resolution

Dependencies Resolved

=========================================================================================================================================================================
 Package                               Arch                       Version                                Repository                                                 Size
=========================================================================================================================================================================
Installing:
 openssh-debuginfo                     x86_64                     8.6p1-1.el7                            /openssh-debuginfo-8.6p1-1.el7.x86_64                      15 M
Updating:
 openssh                               x86_64                     8.6p1-1.el7                            /openssh-8.6p1-1.el7.x86_64                               2.9 M
 openssh-clients                       x86_64                     8.6p1-1.el7                            /openssh-clients-8.6p1-1.el7.x86_64                       2.3 M
 openssh-server                        x86_64                     8.6p1-1.el7                            /openssh-server-8.6p1-1.el7.x86_64                        1.1 M
Updating for dependencies:
 openssl                               x86_64                     1:1.0.2k-21.el7_9                      updates                                                   493 k
 openssl-libs                          x86_64                     1:1.0.2k-21.el7_9                      updates                                                   1.2 M

Transaction Summary
=========================================================================================================================================================================
Install  1 Package
Upgrade  3 Packages (+2 Dependent packages)

Total size: 22 M
Total download size: 1.7 M
Is this ok [y/d/N]: y
Downloading packages:
Delta RPMs disabled because /usr/bin/applydeltarpm not installed.
(1/2): openssl-1.0.2k-21.el7_9.x86_64.rpm                                                                                                         | 493 kB  00:00:02     
(2/2): openssl-libs-1.0.2k-21.el7_9.x86_64.rpm                                                                                                    | 1.2 MB  00:00:03     
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Total                                                                                                                                    527 kB/s | 1.7 MB  00:00:03     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Updating   : 1:openssl-libs-1.0.2k-21.el7_9.x86_64                                                                                                                1/11 
  Updating   : openssh-8.6p1-1.el7.x86_64                                                                                                                           2/11 
new rpm before install
backup old rpm config
1./etc/ssh/sshd_config backup done.
2./etc/pam.d/sshd backup done.
  Updating   : openssh-server-8.6p1-1.el7.x86_64                                                                                                                    3/11 
new rpm after install
backup new config and restore old config
backup new config done
restore old config done and adjust PermitRootLogin
remove all ssh_host_*
  Updating   : openssh-clients-8.6p1-1.el7.x86_64                                                                                                                   4/11 
  Updating   : 1:openssl-1.0.2k-21.el7_9.x86_64                                                                                                                     5/11 
  Installing : openssh-debuginfo-8.6p1-1.el7.x86_64                                                                                                                 6/11 
  Cleanup    : openssh-clients-6.6.1p1-31.el7.x86_64                                                                                                                7/11 
  Cleanup    : openssh-server-6.6.1p1-31.el7.x86_64                                                                                                                 8/11 
  Cleanup    : openssh-6.6.1p1-31.el7.x86_64                                                                                                                        9/11 
  Cleanup    : 1:openssl-1.0.1e-60.el7.x86_64                                                                                                                      10/11 
  Cleanup    : 1:openssl-libs-1.0.1e-60.el7.x86_64                                                                                                                 11/11 
  Verifying  : openssh-server-8.6p1-1.el7.x86_64                                                                                                                    1/11 
  Verifying  : openssh-clients-8.6p1-1.el7.x86_64                                                                                                                   2/11 
  Verifying  : openssh-8.6p1-1.el7.x86_64                                                                                                                           3/11 
  Verifying  : openssh-debuginfo-8.6p1-1.el7.x86_64                                                                                                                 4/11 
  Verifying  : 1:openssl-1.0.2k-21.el7_9.x86_64                                                                                                                     5/11 
  Verifying  : 1:openssl-libs-1.0.2k-21.el7_9.x86_64                                                                                                                6/11 
  Verifying  : 1:openssl-libs-1.0.1e-60.el7.x86_64                                                                                                                  7/11 
  Verifying  : openssh-6.6.1p1-31.el7.x86_64                                                                                                                        8/11 
  Verifying  : 1:openssl-1.0.1e-60.el7.x86_64                                                                                                                       9/11 
  Verifying  : openssh-server-6.6.1p1-31.el7.x86_64                                                                                                                10/11 
  Verifying  : openssh-clients-6.6.1p1-31.el7.x86_64                                                                                                               11/11 

Installed:
  openssh-debuginfo.x86_64 0:8.6p1-1.el7                                                                                                                                 

Updated:
  openssh.x86_64 0:8.6p1-1.el7                       openssh-clients.x86_64 0:8.6p1-1.el7                       openssh-server.x86_64 0:8.6p1-1.el7                      

Dependency Updated:
  openssl.x86_64 1:1.0.2k-21.el7_9                                                 openssl-libs.x86_64 1:1.0.2k-21.el7_9                                                

Complete!
```

