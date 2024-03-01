# Cloudpods QEMU及libqemuio.a编译过程

**注意：以下流程适合CentOS，其他发行版请酌情变更安装包和命令**

``` bash
# 下载官方qemu压缩包
$ wget https://download.qemu.org/qemu-4.2.0.tar.xz

# 解压qemu压缩包
$ tar jxvf qemu-4.2.0.tar.xz

# 下载4.2 patch 文件
$ wget https://raw.githubusercontent.com/yunionio/qemu/main/0002-patch-for-4.2.patch

# 进入qemu目录
$ cd qemu-4.2.0/ 

# 打上补丁文件
$ patch -p1 -i ../0002-patch-for-4.2.patch

# 安装基础包
yum install -y gcc make bison

# 配置, 根据提示安装对应的包，部分包需要自行编译
./configure --prefix=/usr/local/qemu-4.2.0 --target-list=$(uname -m)-softmmu --enable-libusb --extra-ldflags=-lrt --enable-spice --enable-rbd --enable-gnutls --disable-libiscsi --disable-smartcard --disable-auth-pam --disable-virglrenderer

# 编译并安装所有二进制到 /usr/local/qemu-4.2.0
make && make install

# 编译libqemuio.a
make libqemuio.a
```
