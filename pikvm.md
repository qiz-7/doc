## 前置配置

> 将内测USB(挨着HDMI)口改为 peripheral 模式,否则无法模拟鼠键

```sh
dtc -I dtb -O dts -o otg.dts /boot/dtb/amlogic/meson-gxl-s905d-phicomm-n1.dtb
```

```sh
dr_mode = "host";			# 搜索
dr_mode = "peripheral";		# 改为
```

```sh
dtc -I dts -O dtb -o otgon.dtb otg.dts
mv otgon.dtb /boot/dtb/amlogic/
```

```sh
vim /boot/uEnv.txt
FDT=/dtb/amlogic/otgon.dtb
```



## 安装PiKVM (方式有二,不可兼得)

---

### 其一 (稍显繁琐)

删除原有python

```sh
apt remove python*
```

编译 python3.10-dev

```sh
apt update
apt install build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev libssl-dev libsqlite3-dev libreadline-dev libffi-dev libbz2-dev
wget https://www.python.org/ftp/python/3.10.0/Python-3.10.0.tgz

tar -xvf Python-3.10.0.tgz
cd Python-3.10.0

./configure --enable-optimizations

make -j$(nproc)
make altinstall

python3.10 --version		# 验证版本信息
```

安装pikvm

```sh
sudo apt install -y git
git clone http://github.com/jacobbar/fruity-pikvm
cd fruity-pikvm
sudo ./install.sh

btrfs subvolume snapshot / /root/00fruity-PiKVM快照		# 添加快照保平安(可选)
```



### 其二 (推荐)

```sh
git clone --depth=1 https://github.com/mofeng-git/One-KVM.git
cd One-KVM
sudo bash install.sh
#第一阶段安装完成需要重启，再进行第二阶段安装
sudo bash install.sh

btrfs subvolume snapshot / /root/00One-KVM快照		# 添加快照保平安(可选)
```



## 其他功能配置 

---

### 挂载MSD

```sh
dd if=/dev/zero of=/root/diska.img bs=1M count=2500
echo "/root/diska.img /var/lib/kvmd/msd  ext4  nofail,nodev,nosuid,noexec,rw,errors=remount-ro,data=journal,X-kvmd.otgmsd-root=/var/lib/kvmd/msd,X-kvmd.otgmsd-user=kvmd  0 1" >> /etc/fstab			# 开机挂载

systemctl daemon-reload
mount -a		# 挂载
```

```sh
#开启MSD功能，到/etc/kvmd/override.yaml修改msd选项为otg
nano /etc/kvmd/override.yaml
systemctl restart kvmd-otg kvmd
```





---

N1的 PiKVM设置说明

```sh
==== PiKVM ====
用户名/密码: sb / wsgsb
文字说明:
·外侧USB连接USB采集卡，对接受控主机视频输出口
·内测USB(靠近hdmi)连接USB双公头，对接受控主机USB接口
·网络，lan口连接局域网，同局域网浏览器输入https://<ip> 进入管理后台
```

