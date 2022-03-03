## Create /etc/local.d/zram.start to autostart with system startup when openRC initialized. (for example 16G zram)
```
#!/bin/bash

modprobe zram

SIZE=16384
echo $(($SIZE*1024*1024)) > /sys/block/zram0/disksize

mkswap /dev/zram0

swapon /dev/zram0 -p 32767
```

## Create /etc/local.d/zram.stop to stop when system shutdown
```
#!/bin/bash

swapoff /dev/zram0

echo 1 > /sys/block/zram0/reset

modprobe -r zram
```

## Chmod these 2 files
```
root# chmod +x /etc/local.d/zram.start
root# chmod +x /etc/local.d/zram.stop
```

## reboot your system
`root# reboot`
