# 使用 Btrfs 文件系统作为 swap 流程

1. 需要注意事项：
    - 内核版本在 5.0 以上；
    - 初始化交换分区的正确方法：创建一个非压缩、非快照子卷来托管文件，cd 到目录，创建一个0长度的文件，使用 chattr 为其设置没有写时复制的属性，并且确保压缩被禁用；
2. 创建具体流程：
    - ```cd /```
    - ```truncate -s 0 swapfile```
    - ```chattr +C swapfile```
    - ```btrfs property set swapfile compression none```
3. 配置具体流程：
    - ```dd if=/dev/zero of=/swapfile bs=1M count=512 status=progress``` : swap为512M；
    - ```chmod 600 /swapfile``` : 设置权限；
    - ```swapon /swapfile``` : 启用 swap；
    - ```nano /etc/fstab``` : 修改 fstab 实现开机启用 swap；
    - ```/swapfile  none    swap    defaults    0   0```
4. 重启完成。
