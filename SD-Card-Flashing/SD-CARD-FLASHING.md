
**Here I will Show You How to Flash the SD-Card**

After creating the SD-Card contents, you will need to flash it. There are couple of files in the sd-card-container folder [link](http://avnet.me/ultra96v2-vitis-ai-1.1-image):

```
BOOT.BIN
image.ub
dpu.xlbin
ULTRA96V2.hwh
rootfs.tar.gz
```

There are couple of programs to flash the sd-card. I used ***Gparted*** for flashing it. 

**Insert the sd-card**

**Open the Gparted program, create two different partitions**
  * Size: 1GB, FileSystem: Fat32, Label: BOOT
  * Size: at least 4GB, FileSystem: Ext4, Label:Rootfs
  
**Copy ``` 1. BOOT.BIN  2. image.ub 3. dpu.xlbin 4. ULTRA96V2.hwh ``` to the BOOT section**

**For the other section (rootfs) use below commands:**
```
$ sudo mount /dev/sdX /media/rootfs
$ sudo tar -C /media/rootfs -xzf rootfs.tar.gz
$ sudo umount /media/rootfs
```
