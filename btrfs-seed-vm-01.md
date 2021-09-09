# 这是如何使用 Btrfs 文件系统 Seed （种子）功能部署 KVM 下的虚拟机

1. 准备磁盘：
    - 创建3个块设备，第一个为后面的 /boot 分区做准备，空间256M，另外两个块设备分别10G；
    - 将较小的块设备作为启动分区 boot，另外两个一样大小（也可以不一样）的块设备，一个叫做 seed，作为种子文件系统，另一个叫做 new ，作为新的根；
2. 为种子文件系统安装系统：
    - 利用 virt-manager 安装虚拟机，只挂载 boot 和 seed 两个设备；
    - 安装好 Archlinux；
3. 准备种子文件系统：
    - 关闭虚拟机；
    - 对 seed 这个块设备执行：```sudo btrfstune -S 1 /dev/<seed>```；
    - 这个时候再启动虚拟机，进入 root 账户后，尝试：```touch a```后，显示系统为只读；
4. 部署新的虚拟机：
    - 关闭虚拟机，将 new 设备添加到虚拟机中后启动；
    - 成功启动后执行 ```lsblk``` 检查谁是 / 目录，发现<seed>是 / ；
    - 这时候尝试用 ```touch a``` 演示写入情况，发现无法写入，因为是只读文件系统；
    - 再执行：```btrfs device add <new> /```，接着继续执行：```mount -o remount,rw /```
    - 执行 ```lsblk``` 发现根目录已经不再是<seed>了；
    - 尝试用 ```touch a``` 演示写入情况，发现可以写入了，因为 <new> 做了 <seed> 的差分。
5. 完成新虚拟机部署后的其他工作：
    - 可以选择分离 <seed> ，使用命令 ```btrfs device remove <seed> /```，这样就能分离，同时种子将 extents 同步给 <new>；
    - 完成后，修改 bootload 的 new root 的 UUID，否则重启是无法找到 new root 的
