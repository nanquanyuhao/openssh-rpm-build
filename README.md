# openssh-rpm-build

## 条件

1. 构建操作系统：`CentOS 7.7`，使用`root`用户操作
2. 依赖软件包
   - `openssh-8.6p1.tar.gz`
   - `x11-ssh-askpass-1.2.4.1.tar.gz`
3. 提前具备安装软件：`yum install rpm-build zlib-devel openssl-devel gcc perl-devel pam-devel`

## 构建过程

1. `root`用户登录，将项目`rpmbuild`文件夹置于`root`家目录
2. 执行`rpmbuild -ba rpmbuild/SPECS/openssh.spec`
3. 文件夹`/root/rpmbuild/RPMS/x86_64`下即构建产物

## 使用说明

