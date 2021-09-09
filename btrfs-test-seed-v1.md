# Btrfs seed 实战：

1. 准备磁盘：
    - 两个磁盘，大小不一；
    - 一个40G，一个60G，作为测试；
    - ```sudo sgdisk --zap-all /dev/vdb```
    - ```sudo sgdisk --zap-all /dev/vdc```
    - ```sudo cfdisk -z /dev/vdb```
    - ```sudo cfdisk -z /dev/vdc```
    - ```sudo mkfs.btrfs /dev/vdb1 -f```
2. 准备种子文件系统：
    - ```sudo mkdir -p /mnt/test```
    - ```sudo mount /dev/vdb1 /mnt/test```
    - ```sudo touch /mnt/test/a```
    - ```sudo touch /mnt/test/b```
    - ```sudo umount /mnt/test```
    - ```sudo btrfstune -S 1 /dev/vdb1```
3. 准备新文件系统并测试：
    - ```sudo btrfs device add /dev/vdc1 /mnt/test -f```
    - ```sudo ls -l /mnt/test``` : 测试目录下是否有 a 和 b
    - ```sudo touch c /mnt/test``` : 是无法写入的
    - ```sudo mount -o remount,rw /mnt/test```
    - ```sudo touch c /mnt/test``` : 能成功写入了
    - ```sudo touch d /mnt/test``` : 能成功写入了
    - ```sudo ls -l /mnt/test``` : 查看是否显示 a b c d
    - ```sudo umount /mnt/test``` : 卸载 /dev/vdb1 种子文件系统
    - ```sudo mount /dev/vdc1 /mnt/test``` : 挂载新的文件系统
    - ```sudo ls -l /mnt/test``` : 查看目录下是否有 a b c d
4. 试验完。
