1. Download the ISO using the GUI (tested on https://cloud-images.ubuntu.com/lunar/current/lunar-server-cloudimg-amd64-disk-kvm.img)
```
cd /mnt/pve/nfs/template/iso/
mv lunar-server-cloudimg-amd64-disk-kvm.img lunar-server-cloudimg-amd64-disk-kvm.qcow2
qemu-img resize lunar-server-cloudimg-amd64-disk-kvm.qcow2 10G
```
1. Create the VM via CLI
```
qm create 5000 --memory 4096 --core 2 --name ubuntu-cloud --net0 virtio,bridge=vmbr0
qm importdisk 5000 /mnt/pve/nfs/template/iso/lunar-server-cloudimg-amd64-disk-kvm.qcow2 cephpool
qm set 5000 --scsihw virtio-scsi-pci --scsi0 cephpool:vm-5000-disk-0
qm set 5000 --ide2 cephpool:cloudinit
qm set 5000 --boot c --bootdisk scsi0
qm set 5000 --serial0 socket --vga serial0
```
3. Expand the VM disk size to a suitable size (suggested 10 GB)

Adding the QEMU Agent

Power up the template/VM you have created
Install the agent with sudo apt update && sudo apt upgrade -y && sudo apt install qemu-guest-agent
Enable the agent with sudo systemctl enable qemu-guest-agent
Reset the machine-id with cat /dev/null > /etc/machine-id


Another command to reset the machine-id: cat /dev/null > /var/lib/dbus/machine-id
Run cloud-init clean
Finally, run shutdown -h now
```
sudo chmod 777 /etc/machine-id
sudo cat /dev/null > /etc/machine-id
sudo chmod 777 /var/lib/dbus/machine-id
sudo cat /dev/null > /var/lib/dbus/machine-id
sudo cloud-init clean
sudo shutdown -h now
```

4. Create the Cloud-Init template 
5. Deploy new VMs by cloning the template (full clone)
