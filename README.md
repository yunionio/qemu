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

## FAQ:

Q1. 为什么使用本地盘时 QEMU 执行特定操作时会报如下错误：Could not open backing file：Failed to get shared "write" lock"

A1: 这是因为编译 qemu 时，编译机内核头文件中定义了 F_OFD_SETLK, 从而启用了本地文件锁，使用本地盘的虚拟机在做快照或热迁移时会触发这个报错。为了避免这个报错，可以注释内核头文件对 F_OFD_SETLK 的宏申明，再重新编译qemu。

```
$ grep -r F_OFD_SETLK  /usr/include/
/usr/include/asm-generic/fcntl.h:#define F_OFD_SETLK    37
/usr/include/asm-generic/fcntl.h:#define F_OFD_SETLKW   38
/usr/include/bits/fcntl-linux.h:# define F_OFD_SETLK    37
/usr/include/bits/fcntl-linux.h:# define F_OFD_SETLKW   38
```
