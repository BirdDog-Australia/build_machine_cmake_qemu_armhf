detailed reference 
https://gist.github.com/AnshulYbd/36d966712d77c0df5474298170029fc6


# build_machine_cmake_qemu_armhf 
Install Qemu on windows https://qemu.weilnetz.de/w64/qemu-w64-setup-20221230.exe

# create empty place holder file 30Gigs
qemu-img create -f qcow2 ubuntu-bdg.img  30G 

# download cd boot loaders
mkdir netboot 
cd netboot 
wget -r -nH -nd -np -R "index.html*" --quiet http://ports.ubuntu.com/ubuntu-ports/dists/xenial/main/installer-armhf/current/images/generic-lpae/netboot/ 
   
# Run install and create file system 
qemu-system-arm -M virt -m 1024   -kernel vmlinuz   -initrd initrd.gz   -drive if=none,file=ubuntu-bdg.img,format=qcow2,id=hd   -device virtio-blk-device,drive=hd   -netdev user,id=mynet   -device virtio-net-device,netdev=mynet   -nographic -no-reboot

# Extract bootable image initrd and vmlinuz to somefolder say boot
qemu-img convert -f qcow2 -O raw ubuntu.img ubuntu-raw.img
sudo losetup /dev/loop0 ubuntu-raw.img
OFFSET=$(($(sudo fdisk -l /dev/loop0 |grep /dev/loop0p1 |awk '{print $3}')*512))
##OFFSET == 1048576
sudo mount -o loop,offset=$OFFSET /dev/loop0 /mnt

# Create a new directory boot for the new kernel and initrd files and copy them into it:
mkdir boot
cp /mnt/initrd.img-4.4.0-210-generic-lpae boot
cp /mnt/vmlinuz-4.4.0-210-generic-lpae boot

# Cleanup
sudo umount /mnt
sudo losetup -d /dev/loop0
rm ubuntu-raw.img


# Boot the image pointing to the extracted boot files, using below command.
qemu-system-arm -M virt -m 1024 -kernel ./boot/vmlinuz-4.4.0-210-generic-lpae   -initrd ./boot/initrd.img-4.4.0-210-generic-lpae   -append "root=/dev/vda2 rootfstype=ext4" -drive if=none,file=ubuntu-bdg.qcow2,format=qcow2,id=hd -device virtio-blk-device,drive=hd -netdev user,id=mynet,hostfwd=tcp::60022-:22 -device virtio-net-device,netdev=mynet -nographic

# Ssh from host / outside to guest qemu
ssh  -p60022 master@hostIP

cmd file can run the attach image qcow2

