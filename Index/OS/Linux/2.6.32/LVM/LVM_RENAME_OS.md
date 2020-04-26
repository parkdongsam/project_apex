```bash
#!/bin/bash
export VGOS="${HOSTNAME}_vgos001"
vgrename temp01_vgos001 $VGOS

sed -i "s/temp01_vgos001/${VGOS}/g" /etc/fstab
sed -i "s/temp01_vgos001/${VGOS}/g" /boot/grub/grub.conf

export VGFS="${HOSTNAME}_vgfs001"
vgrename temp01_vgfs001 $VGFS
sed -i "s/temp01_vgfs001/${VGFS}/g" /etc/fstab

cp /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak
dracut -f
```
> root 영역을 LVM으로 구성한 후, 새로운 이름으로 변경이 필요할때 사용한다.
