## Create /etc/local.d/zram.start to autostart with system startup when openRC initialized. (for example 16G zram)
```
#!/bin/bash

modprobe zram num_devices=2

SIZE=16384
cat /sys/block/zram0/max_comp_streams
echo $(($SIZE*1024*1024)) > /sys/block/zram0/disksize
echo $(($SIZE*1024*1024)) > /sys/block/zram0/mem_limit
echo lz4 > /sys/block/zram0/comp_algorithm

cat /sys/block/zram1/max_comp_streams
echo $(($SIZE*1024*1024)) > /sys/block/zram1/disksize
echo $(($SIZE*1024*1024)) > /sys/block/zram1/mem_limit
echo lz4 > /sys/block/zram1/comp_algorithm

mkswap /dev/zram0
mkfs.xfs /dev/zram1 

swapon /dev/zram0 -p 32767
mount /dev/zram1 /tmp
```

## Create /etc/local.d/zram.stop to stop when system shutdown
```
#!/bin/bash

swapoff /dev/zram0
umount /dev/zram1

echo 1 > /sys/block/zram0/reset
echo 1 > /sys/block/zram1/reset

modprobe -r zram
```

## Chmod these 2 files
```
root# chmod +x /etc/local.d/zram.start
root# chmod +x /etc/local.d/zram.stop
```

## Adding local service to startup(local service is enable by default for Gentoo, but for Alpine Linux user should do it)
`root# rc-update add local default`

## restart local service
`root# rc-service local restart`
