# qemu

## 编译libqemuio.a

``` bash
# clone 官方qemu代码
$ git clone -b stable-4.2 https://gitlab.com/qemu-project/qemu.git

# 下载4.2 patch 文件
$ wget https://raw.githubusercontent.com/yunionio/qemu/stable-4.2/0001-patch-for-4.2.patch

# 进入qemu目录并打上补丁
$ cd qemu 

# 清理submodule
$ for dir in `git submodule | awk '{print $2}'`; do eval "rm -rf $dir"; done

# 更新submodule, 需要外网连接, 此步骤不可跳过
$ git submodule update

# 打上补丁文件
$ git apply ../0001-patch-for-4.2.patch


# 使用 docker 编译 libqemuio.a
$ docker run --network host -v $(pwd):/root/qemu -it debian:10
root@ampere:/$ sed -i 's|http://deb.debian.org|http://mirrors.aliyun.com|g' /etc/apt/sources.list
root@ampere:/$ apt-get update 
root@ampere:/$ apt-get install -y gcc make git vim python pkg-config flex bison libpixman-1-dev libudev-dev libaio-dev libcurl4-openssl-dev zlib1g-dev libglib2.0-dev libusb-1.0-0-dev libusbredirparser-dev libusbredirhost-dev libcapstone-dev libcephfs-dev librbd-dev librados-dev libspice-server-dev libspice-protocol-dev libfdt-dev
root@ampere:/$ cd /root/qemu/src && make clean
root@ampere:~/qemu/src$ ./configure --target-list=aarch64-softmmu --enable-libusb --extra-ldflags=-lrt --enable-spice --enable-rbd
root@ampere:~/qemu/src$ make libqemuio.a

# 然后退出 docker，libqemuio.a 已经编译在 qemu/src 目录里面了
```
