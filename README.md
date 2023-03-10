# build_machine_cmake_qemu_armhf

Install Qemu on windows
https://qemu.weilnetz.de/w64/qemu-w64-setup-20221230.exe

create empty place holder file 30Gigs
qemu-img create -f qcow2 ubuntu-bdg.img  30G 

Run install and create file system 
qemu-system-arm -M virt -m 1024   -kernel vmlinuz   -initrd initrd.gz   -drive if=none,file=ubuntu-bdg.img,format=qcow2,id=hd   -device virtio-blk-device,drive=hd   -netdev user,id=mynet   -device virtio-net-device,netdev=mynet   -nographic -no-reboot

Boot the image using below command.
qemu-system-arm -M virt -m 1024 -kernel ./boot/vmlinuz-4.4.0-210-generic-lpae   -initrd ./boot/initrd.img-4.4.0-210-generic-lpae   -append "root=/dev/vda2 rootfstype=ext4" -drive if=none,file=ubuntu-bdg.qcow2,format=qcow2,id=hd -device virtio-blk-device,drive=hd -netdev user,id=mynet,hostfwd=tcp::60022-:22 -device virtio-net-device,netdev=mynet -nographic

Ssh from host / outside to guest qemu
ssh  -p60022 master@<hostIP>

cmd file can run the attach image qcow2
